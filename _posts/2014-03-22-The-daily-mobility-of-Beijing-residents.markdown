---
title: The daily mobility of Beijing residents
layout: post
guid:
comments: true
tags:
  - evolution
  - data mining
---


Participating in a research project supported by a Chinese Mobile company, we got access to the mobile data of 10,000 users (randomly sampled from 20 million users). Here we present some results of data explorations in the data set 20131231. 

### The spatial and traffic distribution of base stations

The distribution of 14,909 base stations:

![basemap](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/basemap.png)

The darkness of the data points is propotional to the logrithm value of traffic of the base stations. 

We fitted the traffic distribution of base stations using the [powerlaw](https://pypi.python.org/pypi/powerlaw) module and found that it is a truncated power-law distribution:

![basetrafficdist](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/basetrafficdist.png)


### The mobility of users in the physical world

For each of the 69,726 users, We calculated the number of unique base stations, the number of repeated visited stations (we constructed the sequence such that any two successive stations are different), and also the total navigated distance. 

The mobility of all users:

![navigation](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/navigation.png)

The distributions of the three variables:

![navigationdist](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/navigationdist.png)

and the scaling relationship between each pair of varibales:

![naviallo](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/naviallo.png)


### The mobility of users in the virtual world

For each base station we calculated the most popular site and display them on the map

![sitemap](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/sitemap.png)

