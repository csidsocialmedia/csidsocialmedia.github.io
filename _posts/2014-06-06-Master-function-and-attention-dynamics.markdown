---
title: Master function and attention dynamics
layout: post
guid:
comments: true
tags:
  - network
---


##Master function and Barabasi-Albert model

In their 1999 Science [paper](http://www.barabasilab.com/pubs/CCNR-ALB_Publications/199910-15_Science-Emergence/199910-15_Science-Emergence.pdf), Barabasi and Albert proposed the network model of preferential attachment. To obtain the degree distribution, they first used "continuous-time mean-field analysis" to track the evolution of the degree of individual nodes and then derive the degree distribution. In subsequent work, Dorogovstev, Mendes and Samukhin ([2000](http://arxiv.org/abs/cond-mat/0004434)), took a different approach, using what they call the “master equation” to obtain rigorous asymptotics for the mean degree of the nodes. The lecture [note](http://economics.mit.edu/files/4624) of Daron Acemoglu and Asu Ozdaglar to explain this method inspired me a lot to analyse the dynamics of attention networks.

First of all, let's analyze BA model as a practice. We assume that 

n is the number of nodes in the network;

k is the degree of nodes;

m is the fixed number of links carried by every newly added node;

p_k is the fraction of nodes of degree k.

Then the probability of a new link attaches to a node of degree k is propotional to kp_k. p_k is the probability that a node of degree k is chosen by random selection. We need to weight it by k because BA model assumes that the probability of a new link connecting a exist node is propotional to its degree. However, after being weighted by k, kp_k is not a "well-defined" probability; it does not add up to 1. So we need to normalize it to 


$$
\frac{kp_k}{\sum_{k=1}^{maxK} kp_k} \,\,\,\,\,   (1)
$$


