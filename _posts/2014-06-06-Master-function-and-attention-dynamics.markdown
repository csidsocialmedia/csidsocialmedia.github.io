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

n is the number of nodes in the network; k is the degree of nodes; m is the fixed number of links carried by every newly added node; p_k is the fraction of nodes with degree k; and p_k,n is the fraction of nodes with degree k when there are n nodes in the network.

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
p_k = \frac{k-1}{2}p_{k-1}-\frac{k}{2}p_k, \,\,\,\,\, (k>m) (6)
$$

and

$$
p_m = 1-\frac{m}{2}p_m, \,\,\,\,\, (k=m),   (7)
$$

from which we derive that

$$
\frac{p_k}{p_{k-1}} = \frac{k-1}{k+2} \,\,\,\,\, (k>m),   (8)
$$

and 

$$
p_m = \frac{2}{m+2} \,\,\,\,\, (k=m).   (9)
$$

To solve p_k, we construct 

$$
\frac{p_k}{p_m} =   \frac{p_{m+1}}{p_{m}} ...\frac{p_{k-1}}{p_{k-2}}\frac{p_k}{p_{k-1}}  \,\,\,\,\,   (10)
$$

and find the solution 

$$
p_k =   \frac{2m(m+1)}{k(k+1)(k+2)}.  \,\,\,\,\,   (11)
$$

Eq.(11) approximates a power-law distribution with exponent equals -3 when k is large. This is the same as the result given by the 1999 [paper]((http://www.barabasilab.com/pubs/CCNR-ALB_Publications/199910-15_Science-Emergence/199910-15_Science-Emergence.pdf)).

<img src="/media/files/2014-06-06-Master-function-and-attention-dynamics/BA.png" height="300px" width="400px" />

The above figure shows the simulation of BA network with three different sizes. The degree distributions of nodes are always consistent with analytical prediction, independent of simulation size. The Python code for simulation is listed as follows:

    import random
    import networkx as nx
    import matplotlib.pyplot as plt
    import collections
    import numpy as np
    from os import listdir
	
    # select random subset
    def _random_subset(seq,m):
        targets=set()
        while len(targets)<m:
            x=random.choice(seq)
            targets.add(x)
        return targets
		
    #BA model
    def BA(n, m):
        G=nx.empty_graph(m)
        targets=list(range(m))
        repeated_nodes=[]
        source=m 
        while source<n: 
            G.add_edges_from(zip([source]*m,targets)) 
            repeated_nodes.extend(targets)
            repeated_nodes.extend([source]*m) 
            targets = _random_subset(repeated_nodes,m)
            source += 1
        return G


##Growing random graph

Now let's analyze a different network model. Everything is the same execpt that the new node randomly select m friends without considering their degrees.

The probability of a new link attaches to a node of degree k is propotional to p_k. We do not need to weight or normalize this variable. 

The total number of new links attaching to nodes with degree k is m*p_k. So we have 

$$
(n+1)p_{k,n+1}-np_{k,n} = \Delta k = mp_{k-1}-mp_k, \,\,\,\,\,   (12)
$$

and

$$
(n+1)p_{m,n+1}-np_{m,n} = \Delta m =  1-mp_m. \,\,\,\,\,   (13)
$$

Now, let's focus on the stationary solutions of these euqations by considering Eq. (5):

$$
p_k = mp_{k-1}-mp_k, \,\,\,\,\, (k>m) (14)
$$

and

$$
p_m =  1-mp_m. \,\,\,\,\, (k=m),   (15)
$$

from which we derive that

$$
\frac{p_k}{p_{k-1}} = \frac{m}{m+1} \,\,\,\,\, (k>m),   (16)
$$

and 

$$
p_m = \frac{1}{m+1} \,\,\,\,\, (k=m).   (17)
$$

From Eq. (16), we find that the ratio of two fractions is independent of degree k, this is true because in our randomly growing network, new nodes do not consider the degree of existed nodes in setting up links. To solve p_k, we construct Eq. (10) again and find the solution 

$$
p_k =   \frac{1}{m+1}(\frac{m}{m+1})^{k-m}.  \,\,\,\,\,   (18)
$$

##Preferential repultion

Now let's consider a "reversed" verion of the BA model, i.e., the probability of obtaining new links are proportional to 1/k of the degree.



##The dynamics of collective attention in answering questions

A successful Q&A community should have two properties: high accepting rate of answers and high coverage rate of questions. To achive high accepting rate, a question should attract the competition between many answers so that a good answer is more likely to emerge. But this conflicts with the other goal: high coverage, which is maximized when each question only gets one answer. 

We suggestion that, to sustain itself, a community should balance between these two goals. To understand this balance, let's consider two extreme cases.

In the first case, the community only cares about the accepting rate. So it will assign all answering effort to some very difficult or controversial questions. The classical BA model can be used to describe this strategy: in clickstream networks containing questions as nodes and successive answering behavior as links, the degree distribution is power-law. A few questions attract most of answers while a lot of questions are not answered satisfyingly. 

On the contrary, the community can achive highest coverage rate by using the "preferential repultion" strategy. This will balance the answers across questions and maximize the coverage.

In real world, the balance between the two goals are archived by the collaboration of two different types of users, "elephants" and "rats". "Elephants" tends to answer questions that have already attracted many answers whereas "rats" tends to answer questions that are seldom answered. 

We show that, a mixture between two strategies at the microscopic level, preferential attachment and repulsion, successfully replicates the distribution of answers across questions in the Stackoverflow dataset, as shown by the following figure.

<img src="/media/files/2014-06-06-Master-function-and-attention-dynamics/comparison.png" height="300px" width="400px" />