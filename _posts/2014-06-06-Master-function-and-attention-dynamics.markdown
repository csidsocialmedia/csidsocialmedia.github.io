---
title: Master function and attention dynamics
layout: post
guid:
comments: true
tags:
  - network
---


##Barabasi-Albert model

In their 1999 Science [paper](http://www.barabasilab.com/pubs/CCNR-ALB_Publications/199910-15_Science-Emergence/199910-15_Science-Emergence.pdf), Barabasi and Albert proposed the network model of preferential attachment. To obtain the degree distribution, they first used "continuous-time mean-field analysis" to track the evolution of the degree of individual nodes and then derive the degree distribution. In subsequent work, Dorogovstev, Mendes and Samukhin ([2000](http://arxiv.org/abs/cond-mat/0004434)), took a different approach, using what they call the “master equation” to obtain rigorous asymptotics for the mean degree of the nodes. The lecture [note](http://economics.mit.edu/files/4624) of Daron Acemoglu and Asu Ozdaglar to explain this method inspired me a lot to analyse the dynamics of attention networks.

First of all, let's analyze BA model as a practice. We assume that 

n is the number of nodes in the network;

k is the degree of nodes;

m is the fixed number of links carried by every newly added node;

p_k is the fraction of nodes with degree k;

p_k,n is the fraction of nodes with degree k when there are n nodes in the network.

Then the probability of a new link attaches to a node of degree k is propotional to kp_k. p_k is the probability that a node of degree k is chosen by random selection. We need to weight it by k because BA model assumes that the probability of a new link connecting a exist node is propotional to its degree. However, after being weighted by k, kp_k is not a "well-defined" probability; it does not add up to 1. So we need to normalize it to 


$$
\frac{kp_k}{\sum_{k=1}^{k_{max}} kp_k} = \frac{kp_k}{2m}. \,\,\,\,\,   (1)
$$

The denominator is actually the expected degree of nodes, which equals to 2m because each node brings m links and each link contributes two degrees. So the total number of new links attaching to nodes with degree k is 


$$
\frac{mkp_k}{\sum_{k=1}^{k_{max}} kp_k} = \frac{kp_k}{2}. \,\,\,\,\,   (2)
$$

At each time step, the number of nodes with degree k changes. The change is composed by two parts: the increase contributed by the nodes moving from degree k-1 to k and and the loss caused by the nodes moving from degree k to k+1. Therefore we can express this change as:

$$
(n+1)p_{k,n+1}-np_{k,n} = \Delta k = \frac{k-1}{2}p_{k-1,n}-\frac{k}{2}p_{k,n}, \,\,\,\,\,   (3)
$$

Eq.(3) hold when k > m. When k = m, there is no nodes moving from degree k-1 to k. The only possible increase of node number comes from the new node. So we revise the expression as

$$
(n+1)p_{m,n+1}-np_{m,n} = \Delta m =  1-\frac{m}{2}p_{m,n}, \,\,\,\,\,   (4)
$$


Now, let's assume that the system has envolved to such an equilibrium state that the degree distribution no more changes, i.e.,


$$
p_{k,n+1} = p_{k,n} = p_k, \,\,\,\,\,   (5)
$$

then we have the function

$$
p_k &=& \frac{k-1}{2}p_{k-1}-\frac{k}{2}p_k, \,\,\,\,\, (k>m) 
p_m &=& 1-\frac{m}{2}p_m, \,\,\,\,\, (k=m),   (6)
$$

from which we derive that

$$
\frac{p_k}{p_{k-1}} = \frac{k-1}{k+2} \,\,\,\,\, (k>m)

p_m = \frac{2}{m+2} \,\,\,\,\, (k=m).   (7)
$$

To solve p_k, we construct 

$$
\frac{p_k}{p_m} =   \frac{p_{m+1}}{p_{m}} ...\frac{p_{k-1}}{p_{k-2}}\frac{p_k}{p_{k-1}}  \,\,\,\,\,   (8)
$$

and find the solution 

$$
p_k =   \frac{2m(m+1)}{k(k+1)(k+2)}.  \,\,\,\,\,   (9)
$$

Eq.(9) approximates a power-law distribution with exponent equals -3 when k is large. This is the same as the result given by the 1999 [paper]((http://www.barabasilab.com/pubs/CCNR-ALB_Publications/199910-15_Science-Emergence/199910-15_Science-Emergence.pdf)).