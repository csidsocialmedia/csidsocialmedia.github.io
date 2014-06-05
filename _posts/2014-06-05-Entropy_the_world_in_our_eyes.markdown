---
title: Entropy: the world in our eyes
layout: post
guid:
comments: true
tags:
  - statistical machenics
---

![entropy](/media/files/2014-06-05-Entropy_the_world_in_our_eyes/entropy.jpg)
<sub>Figure by [Kyle Szostek](http://vippasstothespiritworld.blogspot.com/2012/10/entropy-explained.html)</sub>


##A piece of codes as the metaphor of universe

    import random
	
	def RandomNum(n):
        m = 0;
        for i in range(n):
            r = random.choice([0,1])
            m += r
        return m
	

The above Python code generates n 0/1 values and sums them up. If we only focus on the output, 
we will have the following observation

<img src="/media/files/2014-06-05-Entropy_the_world_in_our_eyes/fre.png" height="300px" width="400px" />

Simple, right? This is our metaphor of universe. 

