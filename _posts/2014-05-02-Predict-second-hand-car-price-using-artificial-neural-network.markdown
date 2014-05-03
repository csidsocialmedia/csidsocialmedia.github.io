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

2. Backpropagation

The backpropagation algorithm (Rumelhart and McClelland, 1986) is used to train ANNs. The idea of the backpropagation algorithm is to minimize the error (difference between actual and expected results) by addjusting the weights on links. In other words, we shall calculate the partial derivative of the error E with respect to each link weight w_i. Using the chain rule we can decompose this target variable to

$$
 \frac{\partial E}{\partial w_i} = \frac{dE}{dy}\frac{dy}{d \mathrm{net}} \frac{\partial \mathrm{net}}{\partial w_i}.   \,\,\,\,\,   (1)
$$


The left side of the euqation shows how the error changes when the weights are changed, and the three items in the right side show how the error changes when the output is changed, how the output changes when the weighted sum changes, and how the weighted sum changes as the weights change, respetively. 

If we define the error function as 

$$
E = \tfrac{1}{2}(t - y)^2, \,\,\,\,\,   (2)
$$

and apply a sigmoid function on neurons to transform the result 

$$
y = tanh (net) = tanh(\sum_{i=1}^{n}w_ix_i)  \,\,\,\,\,   (3)
$$

Then we have 

$$
\frac{dE}{dy} = t - y,  \,\,\,\,\,   (4)
$$

and 

$$
\frac{\partial \mathrm{net}}{\partial w_i} = \frac{\partial \sum_{i=1}^{n}w_ix_i}{\partial w_i} = x_i,  \,\,\,\,\,   (5)
$$

and 

$$
\frac{dy}{d\mathrm{net}} = \frac{d}{d\mathrm{net}} tanh = sech^2(y) = 1-y^2+(2 y^4)/3 + O(y)^5\approx 1-y^2.  \,\,\,\,\,   (6)
$$

put Eq.(4) ~ Eq.(6) together, we have 


$$
 \frac{\partial E}{\partial w_i} = (t-y)(1-y^2)x_i.   \,\,\,\,\,   (7)
$$

To update the weight w_i using gradient descent, we should define a learning rate N. 


$$
\Delta w_i = -N \frac{\partial E}{\partial w_i} = N(y-t)(1-y^2)x_i.   \,\,\,\,\,   (8)
$$


In most of cases we also need and a momentum M to store the direction and magnitude of the last change to avoid being trapped in the local minima of error lanscape. We set N=0.5, M=0.1 in the following code.

    
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

As shown by the above figure, age and odometer are non-linearly, negatively correlated with price. Neural network is a good tool to model this non-linear realtionship. This primary step of data visualization/exploration is important for furture analysis. For example, I found in this step that some seller use k miles as unit for odometer, while others use miles. Therefore, data should be always be exmained/cleaned before we applying statistical models. 

4. Future directions worth studying

Can we predict the price from the images using neural network model ? 



