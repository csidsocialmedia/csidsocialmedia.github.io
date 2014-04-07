---
title: A Recommendation System based on the Yelp! Data
layout: post
guid:
comments: true
tags:
  - data mining
  - PCI group
---

Recently, Yelp! released a [data set](http://www.yelp.com/dataset_challenge) for academic research. This data sets includes the 335,022 reviews from 70,817 users to 15,585 businesses from greater Phoenix, AZ metropolitan area. In [this post](http://csidsocialmedia.github.io/2014/03/21/Yelp-data-show-the-popularity-of-Tempe-restaurants.html) I analyzed the spatial distribution of restaurants. Now we are going to construct a recommending engine using this data set.

Several functions (eg., topMatches and calculateSimilarItems) used here are cited and revised from Chapter 2 of [this book](http://shop.oreilly.com/product/9780596529321.do). And we should make some corrections before using the code of the book:

	+ 1. On page 21, line 4, "for p2 in get_urlposts(p1['href'])" should be "for p2 in 
	get_urlposts(p1['url'])". This is because the site delicious has changed the variable 
	name in API.
	
	+ 2. On page 21, line 5, after "user=p2['user']" we'd better add "if user:" and also 
	adjust the indent of the following lines. This is because some times the API will 
	return a empty space of user, which we do not want to consider in analysis. 
	
	+ 3. The user-based Delicious recommendation engine may not work well if we are 
	calculating the similarit between two users who do not share any common url.

The idea of user-based recommedation: an typical user-based recommending algorithm calculates the similarity between all pairs of users (from their shared items), and predicts the preference (e.g., a rate between 1~5 ) of a user  i  on an item using the expected value of the rates of other users j weighted by the similarity between i and j.  Therefore, if a user rates on more resources, he will have impact on more people, because the similarity weights carried by his rates are higher (he shares common items with more users). The above idea can be expressed as

$$
logit(Y_{ij} = 1) = ln(\frac{p_{ij}}{1-p_{ij}}) = {\theta }^{T} \mathbf{\delta} [g(\mathbf{y}, \mathbf{X})]_{ij}
$$

There two types of recommendatons, item-based and user-based. They share the similar idea of estimating the epected value of preference from a distribution. (to be continued) 
 

Python codes are listed as follows:

    #modules
    import re
    import random
    import numpy as np
    import matplotlib.cm as cm
    import statsmodels.api as sm
    from collections import Counter
    from collections import defaultdict
    from scipy.stats.stats import pearsonr
    import matplotlib.pyplot as plt
    from math import sqrt
    import time
    import json
    from scipy import spatial
	
    # functions
    def sim_pearson(prefs,p1,p2):
        a = [(i, prefs[p1][i], prefs[p2][i]) for i in prefs[p1] if i in prefs[p2]]
        if a and len(a) > 1:
            b = np.array(a)[:,1:].astype(float)
            return pearsonr(b[:,0], b[:,1])[0]
        else:
            return 0
    
    def topMatches(prefs,p1,n=5, similarity=sim_pearson):
        scores=[(similarity(prefs,p1,other),other)
                       for other in prefs if other!=p1]
        scores.sort()
        scores.reverse( )
        return scores[0:n]
    
    def calculateSimilarItems(prefs,n=10):
        result={}
        c=0
        for item in prefs:
            c+=1
            if c%1000==0: print "%d / %d" % (c,len(prefs))
            scores=topMatches(prefs,item,n=n,similarity=sim_pearson)
            result[item]=scores
        return result
	
    # main dataset
    user_dict={}
    business_dict={}
    with open('/Users/csid/Documents/bigdata/yelp/yelp_academic_dataset_review.json') as f:
        for line in f:
            line = json.loads(line)
            user = str(line['user_id'])
            business = str(line['business_id'])
            rate = line['stars']
            if business not in business_dict:
                business_dict[business]={}
            else:
                business_dict[business][user]=rate
            if user not in user_dict:
                user_dict[user]={}
            else:
                user_dict[user][business]=rate
	
    #prepare item similarity matrix, this may takes a while
    itemsim=calculateSimilarItems(business_dict)
	
    #hot businesses to be recommended when there is no record
    topshops={}
    for i in business_dict:
        rates=business_dict[i].values()
        mean=np.mean(rates)
        if mean ==5:
            topshops[i]=len(rates)
    topshops = [(score,item) for item,score in topshops.items()]
    topshops.sort()
    topshops.reverse()
    topshops = [(5.0,j) for i,j in topshops[:5]]
	
    #match user and business information
    B = {}
    with open('/Users/csid/Documents/bigdata/yelp/yelp_academic_dataset_business.json') as f:
        for line in f:
            line = json.loads(line)
            bid = line["business_id"]
            ad  = line["full_address"]
            B[bid] = ad
    U = {}
    with open('/Users/csid/Documents/bigdata/yelp/yelp_academic_dataset_user.json') as f:
        for line in f:
            line = json.loads(line)
            uid = line["user_id"]
            name  = line["name"]
            U[uid] = name
	
    def getRecommendedItems(prefs,itemMatch,user):
        userRatings=prefs[user]
        scores={}
        totalSim={}
        # Loop over items rated by this user
        for (item,rating) in userRatings.items():
                 # Loop over items similar to this one
                 for (similarity,item2) in itemMatch[item]:
                        # Ignore if this user has already rated this item
                        if item2 in userRatings: continue
                        # Weighted sum of rating times similarity
                        scores.setdefault(item2,0)
                        scores[item2]+=similarity*rating
                        # Sum of all the similarities
                        totalSim.setdefault(item2,0)
                        totalSim[item2]+=similarity
        # Divide each total score by total weighting to get an average 
        rankings=[(score/totalSim[item],item) for item,score in scores.items() if score>0]
        # Return the rankings from highest to lowest 
        rankings.sort( )
        rankings.reverse( )
        if rankings:
            return  [(i,B[j]) for i,j in rankings[:5]]
        else:
            return  [(i,B[j]) for i,j in topshops]
	
    # demo
    getRecommendedItems(user_dict,itemsim,random.sample(user_dict.keys(),1)[0]) 



