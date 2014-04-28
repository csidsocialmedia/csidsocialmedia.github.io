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

We fitted the following discussed long-tail distributions using [powerlaw](https://pypi.python.org/pypi/powerlaw) module.

### The mobility of users in the physical and virtual world 

For each of the 69,726 users, We calculated the number of unique base stations, the number of repeated visited stations (we constructed the sequence such that any two successive stations are different), and also the total navigated distance. 

The mobility of all users:

![navigation2](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/navigation2.png)

The distributions of the six variables:

![ndist.png](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/ndist.png)

and the scaling relationship between several pairs of varibales:

![allometric.png](/media/files/2014-03-22-The-daily-mobility-of-Beijing-residents/allometric.png)


