---
title: Predict second hand car price using artificial neural network
layout: post
guid:
comments: true
tags:
  - PCI group
---

1. The artificial neural network in a nutshell

![demo](/media/files/2014-05-02-Predict-second-hand-car-price-using-artificial-neural-network/demo.png)


    
    class NN:
        def __init__(self, li, lh, lo):
            # number of nodes in the input, hidden, and output layers
            self.li = li + 1 
            self.lh = lh
            self.lo = lo
            # initiate the activations for nodes
            self.ai, self.ah, self.ao = map(np.ones, [self.li, self.lh, self.lo])
            # initiate the weights of links from input nodes to hidden nodes
            self.wi = np.random.random((self.li, self.lh))*2-1
            # initiate the weights of links from hidden nodes to output nodes
            self.wo = np.random.random((self.lh, self.lo))*2-1
            # initiate the last change in weights for momentum   
            self.ci = np.zeros((self.li, self.lh))
            self.co = np.zeros((self.lh, self.lo))
            
        def sigmoid(self,x):
            return np.tanh(x)    
    
        def dsigmoid(self,y):
            return 1.0 - np.square(y)
            
        def forward(self, inputs):
            iputs = np.array(inputs)
            if len(inputs) != self.li-1:
                raise ValueError('wrong number of inputs')
            # input activations
            # self.ai[:-1] = map(sigmoid,inputs)
            self.ai[:-1] = inputs 
            # hidden activations
            for j in range(self.lh):
                self.ah[j] = self.sigmoid(sum(self.ai * self.wi[:,j]))
            # output activations
            for k in range(self.lo):
                self.ao[k] = self.sigmoid(sum(self.ah * self.wo[:,k]))
            return self.ao
        
        def backPropagate(self, targets, N, M): # N: learning rate = 0.5   M: momentum factor = 0.1
            targets = np.array(targets)
            if len(targets) != self.lo:
                raise ValueError('wrong number of target values')
            # calculate error terms for output
            output_d = self.dsigmoid(self.ao) * (targets-self.ao)
            # calculate error terms for hidden
            hidden_d = np.zeros(self.lh)
            for j in range(self.lh):
                hidden_d[j] = self.dsigmoid(self.ah[j]) * sum( output_d * self.wo[j,:])
            # update output weights
            for j in range(self.lh):
                for k in range(self.lo):
                    change = output_d[k]*self.ah[j]
                    self.wo[j][k] = self.wo[j][k] + N*change + M*self.co[j][k]
                    self.co[j][k] = change
                    #print N*change, M*self.co[j][k]
            # update input weights
            for i in range(self.li):
                for j in range(self.lh):
                    change = hidden_d[j]*self.ai[i]
                    self.wi[i][j] = self.wi[i][j] + N*change + M*self.ci[i][j]
                    self.ci[i][j] = change
            # calculate error
            error = sum( 0.5 * np.square(targets - self.ao) )
            return error
    
        def test(self, patterns):
            for p in patterns:
                print(p[0], '->', self.forward(p[0]))
    
        def train(self, patterns, iterations=1000, N=0.5, M=0.1):
            for i in range(iterations):
                error = 0.0
                for p in patterns:
                    inputs = p[0]
                    targets = p[1]
                    self.forward(inputs)
                    error = error + self.backPropagate(targets, N, M)
                if i % 100 == 0:
                    print('error %-.5f' % error)
    
    
    def demo():
        # Teach network XOR function
        pat = [
            [[0,0], [0]],
            [[0,1], [1]],
            [[1,0], [1]],
            [[1,1], [0]]
        ]
    
        # create a network with two input, two hidden, and one output nodes
        n = NN(2, 2, 1)
        # train it with some patterns
        n.train(pat)
        # test it
        n.test(pat)

2. Collect data from craigslist

We should collect and save a list of posts at first

    import urllib2
    import urllib
    from bs4 import *
    import re
    from datetime import datetime
    from collections import defaultdict
    import matplotlib.pyplot as plt
    import numpy as np
    import math
    import random
    import string
	
    # refreshing results
    def flushPrint(variable):
        sys.stdout.write('\r')
        sys.stdout.write('%s' % variable)
        sys.stdout.flush()
    
    def getUrls():
        P = {}
        ns = range(0,40100,100)  
        # ns = range(0,1000,100) small number of pages for test
        for i in ns:
            # counter
            flushPrint(i)
            # end of counter
            url = 'https://phoenix.craigslist.org/cto/index' + str(i) + '.html'
            soup=BeautifulSoup(urllib2.urlopen(url).read())
            s=soup.body.findAll('p', {"class" : "row"})
            for i in s:
                try:
                    postId = 'https://phoenix.craigslist.org' + i('a')[0]['href']
                    P[postId] = 1
                except:
                    pass
        return P
	
	P = getUrls()
    with open('/Users/csid/Documents/research/carRecommender/postIds.txt','w') as f:
        for i in P:
            f.write(i+'\n')

Then we retrieve key features that may related to the price of the car

    Q={}
    with open('/Users/csid/Documents/research/carRecommender/postIds.txt','r') as f:
        for i in f:
            Q[i.strip()]=[]
	
    def scraper(url):
        try:
            soup=BeautifulSoup(urllib2.urlopen(url).read())
            keywords = soup.body.find('div', {"class" : "mapAndAttrs"})('b')
            if len(keywords)==4:
                condition,name, odometer, title = [str(i.text) for i in keywords]       
                location = url.split('/')[-2]
                time=soup.body.find('div', {"class" : "postinginfos"})('p')[1]('time')[0]['datetime']
                pricetext=soup.body.find('h2', {"class" : "postingtitle"}).text.split('$')[1]
                price = str(re.sub("\D", "", pricetext))
                return [time, name, price, condition, odometer, title, location]
        except:
            return []
	
    W={}
    for i in Q.keys():
        flushPrint(Q.keys().index(i))
        s = scraper(i)
        if s:
            W[i]=s

we also want to collect the images 

    with open('/Users/csid/Documents/research/carRecommender/postData.txt','w') as f:
        for i in W:
            f.write(i+'\t'+'\t'.join(W[i])+'\n')
    Q={}
    with open('/Users/csid/Documents/research/carRecommender/postData.txt','r') as f:
        for line in f:
            line=line.strip().split("\t")
            Q[line[0]]=line[1:]
    # download image
    sa = '/Users/csid/Documents/research/carRecommender/images/'
    for i in Q.keys():
        flushPrint(Q.keys().index(i))
        try:
            s=urllib2.urlopen(i).read()
            imagelink = s.split(r'imgList = ["')[1].split(r'",')[0]
            sv = sa + re.sub("/","_",i[31:-5]) + '.jpg'
            a, b = urllib.urlretrieve(imagelink,sv)
            Q[i].append('True')
        except:
            Q[i].append('False')

In our data set, we have 40,063 posts, 13697 of which contain full information in terms of the features to be studied.

3. Explorating the data

![carprice](/media/files/2014-05-02-Predict-second-hand-car-price-using-artificial-neural-network/carprice.png)

As shown by the above figure, age and odometer are non-linearly, negatively correlated with price. Neural network is a good tool to model this non-linear realtionship.

4. Future directions worth studying

Can we predict the price from the images using neural network model ? 



