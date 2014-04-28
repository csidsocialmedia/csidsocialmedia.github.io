---
title: Search engine in nutshell
layout: post
guid:
comments: true
tags:
  - data mining
  - PCI group
---

![crawler.jpg](/media/files/2014-04-18-Search-engine-in-nutshell/crawler.jpg)
<sub>Figure cited from [here](http://www.417marketing.com/how-do-web-crawlers-work/)</sub>

1. Three steps to build a search engine

There are at least three steps to buld a (toy) search engine:

(1) Crawling and saving pages;

(2) Ranking pages for query words;

(3) Optimizing the result

This class will cover the first two parts and also some contents of the last but not least part.

2. Build a crawler 

The following python scripts build a crawler 

    import urllib2
    from bs4 import *
    from urlparse import urljoin
    import sqlite3
    import re
    import sys
	
	ignorewords=set(['the','of','to','and','a','in','is','it'])
    
    def flushPrint(constant, variable):
        sys.stdout.write('\r')
        sys.stdout.write(constant + ' %s' % variable)
        sys.stdout.flush()    
    
    class crawler:
        #=======================prepare a SQL database and customize some SQL functions====================
        # create/open a database 
        def __init__(self,dbname):
            self.con = sqlite3.connect(dbname)
        # initialize the database
        def createindextables(self):
            self.con.execute('create table urllist(url)')
            self.con.execute('create index urlidx on urllist(url)')
            self.con.execute('create table wordlist(word)')
            self.con.execute('create index wordidx on wordlist(word)')
            self.con.execute('create table wordlocation(urlid,wordid,location)')
            self.con.execute('create index wordurlidx on wordlocation(wordid)')
            self.con.execute('create table link(fromid integer,toid integer)')
            self.con.execute('create index urlfromidx on link(fromid)')
            self.con.execute('create index urltoidx on link(toid)')
            self.con.execute('create table linkwords(wordid,linkid)')
            self.con.commit()
        # close the database
        def __del__(self): 
            self.con.close()
        # query/add an item (url,word) in the database and return its row id (in python style)
        def getentryid(self,table,field,value,createnew=True):
            u=self.con.execute("select rowid from %s where %s='%s'" % (table,field,value)).fetchone()
            if u==None: 
                return self.con.execute("insert into %s (%s) values ('%s')" % (table,field,value)).lastrowid
            else: 
                return u[0]
        # check if a url has already been indexed (in python style)
        def isindexed(self,url):
            u=self.con.execute("select rowid from urllist where url= '%s'" % url).fetchone() 
            if u!=None:
                v=self.con.execute("select * from wordlocation where urlid=%d" % u[0]).fetchone() 
                if v!=None: return True
            return False
        #=========================collect web data====================
        # get text from a web page (a brilliant trick using a recursive function!)
        def gettextonly(self,soup):
            v=soup.string
            if v==None:
                c=soup.contents
                resulttext=''
                for t in c:
                    subtext=self.gettextonly(t)
                    resulttext+=subtext+'\n'
                return resulttext
            else:
                return v.strip()
        # function to cut text into words
        def separatewords(self,text):
            splitter=re.compile('\\W*')
            return [s.lower() for s in splitter.split(text) if s!='']
        # add the url and the words of a page to the database
        def addtoindex(self,url,soup):
            if self.isindexed(url): return
            flushPrint('Indexing', url)
            text=self.gettextonly(soup)
            words=self.separatewords(text)
            urlid=self.getentryid('urllist','url',url)
            for i in range(len(words)):
                word=words[i]
                if word in ignorewords: continue
                wordid=self.getentryid('wordlist','word',word)
                self.con.execute("insert into wordlocation(urlid,wordid,location) values (%d,%d,%d)" % (urlid,wordid,i))
        # add link between two pages
        def addlinkref(self,urlFrom,urlTo,linkText):
            pass
        # the cralwer 
        def crawl(self,pages,depth=2):
            for i in range(depth):
                newpages=set()
                for page in pages:
                    try:
                        c=urllib2.urlopen(page)
                    except:
                        flushPrint('Could not open', page)
                        continue
                    soup=BeautifulSoup(c.read()) 
                    self.addtoindex(page,soup)
                    links=soup('a')
                    for link in links:
                        if 'href' in dict(link.attrs):
                            url=urljoin(page,link['href'])
                            if url.find("'")!=-1: continue
                            url=url.split('#')[0]  # remove location portion
                            if url[0:4]=='http' and not self.isindexed(url):
                                newpages.add(url)
                            linkText=self.gettextonly(link)
                            self.addlinkref(page,url,linkText)
                    self.con.commit()
                pages=newpages
        #=========================calculate page rank====================
        def calculatepagerank(self,iterations=20):
            # clear out the current PageRank tables
            self.con.execute('drop table if exists pagerank')
            self.con.execute('create table pagerank(urlid primary key,score)')
            # initialize every url with a PageRank of 1
            self.con.execute('insert into pagerank select rowid, 1.0 from urllist') 
            self.con.commit()
            for i in range(iterations):
                print "Iteration %d" % (i)
                for (urlid,) in self.con.execute('select rowid from urllist'):
                    pr=0.15
                    # Loop through all the pages that link to this one
                    for (linker,) in self.con.execute('select distinct fromid from link where toid=%d' % urlid):
                        # Get the PageRank of the linker
                        linkingpr=self.con.execute('select score from pagerank where urlid=%d' % linker).fetchone()[0]
                        # Get the total number of links from the linker 
                        linkingcount=self.con.execute('select count(*) from link where fromid=%d' % linker).fetchone()[0] 
                        pr+=0.85*(linkingpr/linkingcount)
                    self.con.execute('update pagerank set score=%f where urlid=%d' % (pr,urlid)) 
                self.con.commit()

2. Ranking pages for query words

The following python scripts combine four different strategies in ranking pages, three of them are content-based (key-word frequency, location, and distance) and the rest one is link-based (page rank).

    class searcher:
        # open and close the database
        def __init__(self,dbname):
            self.con=sqlite3.connect(dbname)
        def __del__(self): 
            self.con.close( )
        # ============================query function=============================
        def getmatchrows(self,q):
            fieldlist='w0.urlid'
            tablelist=''
            clauselist=''
            wordids=[]
            words=q.split(' ')
            tablenumber=0
            for word in words:
                wordrow=self.con.execute("select rowid from wordlist where word='%s'" % word).fetchone() 
                if wordrow!=None:
                    wordid=wordrow[0]
                    wordids.append(wordid)
                if tablenumber>0:
                    tablelist+=','
                    clauselist+=' and '
                    clauselist+='w%d.urlid=w%d.urlid and ' % (tablenumber-1,tablenumber)
                fieldlist+=',w%d.location' % tablenumber
                tablelist+='wordlocation w%d' % tablenumber
                clauselist+='w%d.wordid=%d' % (tablenumber,wordid)
                tablenumber+=1
            fullquery='select %s from %s where %s' % (fieldlist,tablelist,clauselist)
            cur=self.con.execute(fullquery)
            rows=[row for row in cur]
            return rows,wordids
        # ============================rank the pages=============================
        # normalize ranking score
        def normalizescores(self,scores,smallIsBetter=0):
            vsmall=0.00001 # Avoid division by zero errors
            if smallIsBetter:
                minscore=min(scores.values( ))
                return dict([(u,float(minscore)/max(vsmall,l)) for (u,l) in scores.items()]) 
            else:
                maxscore=max(scores.values( ))
                if maxscore==0: maxscore=vsmall
                return dict([(u,float(c)/maxscore) for (u,c) in scores.items()])
        #---------------------------content based ranking-------------------------
        # ranking pages by key-word frequency
        def frequencyscore(self,rows):
            counts=dict([(row[0],0) for row in rows])
            for row in rows: counts[row[0]]+=1
            return self.normalizescores(counts)
        # ranking pages by key-word location
        def locationscore(self,rows):
            locations=dict([(row[0],1000000) for row in rows])
            for row in rows:
                loc=sum(row[1:])
                if loc<locations[row[0]]: locations[row[0]]=loc
            return self.normalizescores(locations,smallIsBetter=1)
        # ranking pages by key-word distance
        def distancescore(self,rows):
            if len(rows[0])<=2: return dict([(row[0],1.0) for row in rows])
            mindistance=dict([(row[0],1000000) for row in rows])
            for row in rows:
                dist=sum([abs(row[i]-row[i-1]) for i in range(2,len(row))])
                if dist<mindistance[row[0]]: mindistance[row[0]]=dist
            return self.normalizescores(mindistance,smallIsBetter=1)
        #---------------------------link based ranking-------------------------
        # page rank
        def pagerankscore(self,rows):
            pageranks=dict([(row[0],self.con.execute('select score from pagerank \
            where urlid=%d' % row[0]).fetchone()[0]) for row in rows]) 
            maxrank=max(pageranks.values( ))
            normalizedscores=dict([(u,float(l)/maxrank) for (u,l) in pageranks.items()]) 
            return normalizedscores
        # cosider a combination of ranking scores
        def getscoredlist(self,rows,wordids):
            totalscores=dict([(row[0],0) for row in rows])
            #weights=[(1.0,self.frequencyscore(rows))]
            weights=[(1.0,self.frequencyscore(rows)),
                     (1.0,self.locationscore(rows)),
                     (1.0,self.distancescore(rows)),
                     (1.0,self.pagerankscore(rows))]
            for (weight,scores) in weights:
              for url in totalscores:
                totalscores[url]+=weight*scores[url]
            return totalscores
        # ============================print the result============================= 
        def geturlname(self,id):
            return self.con.execute("select url from urllist where rowid=%d" % id).fetchone()[0]
        def query(self,q):
            rows,wordids=self.getmatchrows(q)
            scores=self.getscoredlist(rows,wordids)
            rankedscores=sorted([(score,url) for (url,score) in scores.items()],reverse=1) 
            for (score,urlid) in rankedscores[0:10]:
                print '%f\t%s' % (score,self.geturlname(urlid))


3. The scripts to test our search engine

The scripts to test crwaler:

    # 2-step depth crawling from www.nytimes.com
    crawler1=crawler('/Users/csid/Documents/research/pci_group/searchindex.db')
    crawler1.createindextables()
    pages=['http://www.nytimes.com']
    crawler1.crawl(pages)
	

The scripts to test the querying function:

    e=searcher('/Users/csid/Documents/research/pci_group/searchindex.db')
    e.query('i love obama')
	
	e.query('i hate obama')

The scripts to calculate page rank:

	
	crawler1=crawler('/Users/csid/Documents/research/pci_group/searchindex.db')
	crawler1.calculatepagerank()