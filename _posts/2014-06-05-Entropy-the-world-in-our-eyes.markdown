---
title: Entropy the world in our eyes
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
	
	def f(n):
        m = 0;
        for i in range(n):
            r = random.choice([0,1])
            m += r
        return m
	

The above Python code generates n 0/1 values and sums them up. If we only focus on the output, 
we will have the following observation

<img src="/media/files/2014-06-05-Entropy_the_world_in_our_eyes/fre.png" height="300px" width="400px" />

As shown by the figure, the most likely result is n/2 (n=100 in this case), and n/2 - 1 or n/2 + 1 is also very likely to be observed. As the values deviate from n/2, they becomes very unlikely to be observed.

Why? Becuse outputs correspond to a different number of combinations. f(n)=0 or f(n)=n corresonds to only one combination: it requires all n variables to be 0 or 1. But f(n)=n/2 corresonds to n!/(n/2)! combinations. So it is easier for us to observe f(n)=n/2.

Generally, the number of the combinations lead to f(n)=k is 

$$
\Omega (k) = \dbinom{k}{n} \frac{n!}{k!(n-k)!}
$$

Simple and straighforward, right? This is our metaphor of universe. 

