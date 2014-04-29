---
title: Gini index and Zipf's law in online communities
layout: post
guid:
comments: true
tags:
  - online community
  - network
---

1. Gini index, Lorenz curve, and Zipf's law

<img src="/media/files/2014-04-29-Gini-index-and-zipf's-law-in-online-communities/gini.png" height="400px" width="400px" />
<sub>Figure cite from [wiki](http://en.wikipedia.org/wiki/Gini_coefficient)</sub>

The Gini coefficient (G) is defined based on the Lorenz curve L(F), which plots the proportion of the total income (y axis) cumulatively earned by the bottom f% (or 0 < F < 1) of the population. In particular, Assume that the area between the line at 45 degrees and the Lorenz curve is A, and the area under the Lorenz curve is B, then G =  A / (A + B). Since A + B = 0.5, we have G = 1 – 2 B. G varies theoretically from 0 (complete equality) to 1 (complete inequality). 

Those who are familiar with Zipf's law would immediately connect it with Lorenz curve. As suggested by [Adamic](http://www.hpl.hp.com/research/idl/papers/ranking/ranking.html), Zipf's law is the inverse function of Pareto distribution, which is obserbed widely in the distribution of incomes. In the following part we will show how to relate the exponent of Zipf's law (beta) to G. 

As mentioned, we have

$$
B = \int_0^1 L(F) dF    (1)
$$

Meanwhile, the cumulative distribution function (CDF) of Pareto distribution is

$$
F = 1 - (\frac{x_m}{x})^(\alpha)   (2)
$$

in which the left side is the bottom k% of the population and the right side is the income x. By inversing the Pareto distribution we get 

$$
x(F) = x_m(1-F)^(-\frac{1}{\alpha})    (3)
$$

where the left sie (y axis) is the income x and right side (x axis) is the ratio F of population. 
Here F is the ratio obtained by sorting the population increasingly. Now the Lorenz curve, which plots total income against the bottom F population, can be expressed as

$$
L(F) = \frac{\int_0^F x(F) dF }} { \int_0^1 x(F) dF } = 1 - (1-F)^(1-\frac{1}{\alpha})  (4)
$$

The following plot shows Eq.(4) with four different values of alpha.  

<img src="/media/files/2014-04-29-Gini-index-and-zipf's-law-in-online-communities/pareto_Lorenz.png" height="400px" width="400px" />
<sub>Figure cite from [wiki](http://en.wikipedia.org/wiki/Gini_coefficient)</sub>

Now we have 

$$
G = 1 – 2 B = 1 - 2(\int_0^1 L(F) dF) = \frac{1}{2\alpha - 1}  (5)
$$


In Eq.(3), if we sort the population decreasingly rather than increasingly, and replace ratio k with real number r, we get the rank-ordered curve

$$
x = x_m(r)^(-\frac{1}{\alpha})   (6)
$$

which is called Zipf's law when the exponent equals 1. Therefore we know the exponent beta of Zipf's law is related to the exponent alpha of pareto distribution by 

$$
\beta = \frac{1}{\alpha}     (7)
$$

By putting together Eq.(7) and Eq.(5) we get 

$$
G = \frac{1}{2\alpha - 1} = \frac{\beta}{2-\beta}   (8)
$$

