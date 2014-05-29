---
title: Flow networks have a purpose
layout: post
guid:
comments: true
tags:
  - network
  - PCI group
---


![usriver1](/media/files/2014-05-28-Flow-networks-have-a-purpose/usriver1.png)

![usriver2](/media/files/2014-05-28-Flow-networks-have-a-purpose/usriver2.png)

![usriver2](/media/files/2014-05-28-Flow-networks-have-a-purpose/usriver3.png)

<sub>Figure by [Nelson](http://www.somebits.com/weblog/tech/vector-tile-river-map.html)</sub>


##Why natural flow networks are so beautiful

The above figures visualize the U.S. river networks using [NHDPlus dataset](http://www.horizon-systems.com/nhdplus/) and [D3.js](http://www.somebits.com/rivers/rivers-d3.html). The amazing beauty of these natural flow systems motivates us to 
ask such questions: why they are so beautiful? Are there universal laws behind these systems, like the law of gravity ?
Or, is there a general "purpose" of these systems, like the principle of [least action](http://en.wikipedia.org/wiki/Principle_of_least_action) ?

[Rinaldo](http://www.image.unipd.it/a.rinaldo/allegati/Minimum_energy.pdf) (1992, 1993) et al. proposed a model called "optimal channel networks" (OCN) to explain the beutiful tree structure of flow networks. A comprehensive literature review on OCN can be found [here](http://abouthydrology.blogspot.com/2012/09/my-past-research-on-evolution-of-river.html). And [this](http://www.pnas.org/content/110/48/19295.abstract) and [this](http://www.pnas.org/content/early/2014/01/31/1322700111) are two recently pulished papers on OCN. 

<img src="/media/files/2014-05-28-Flow-networks-have-a-purpose/3flownetworks.png" height="500px" width="350px" />
<sub>In the second plot there are two versions of global cost, depending on whether we put the futher points on the same line as the closer points.</sub>

The above figure is cited from [here](http://onlinelibrary.wiley.com/doi/10.1029/91WR03034/abstract). The authors used this demo to 
explain the formation of river trees. They suggest a flow network has to minimize both of the global and local cost (in terms of flow dissipation, or energy dissipation cosidering potential energy, slope of basin, and many other factors). The spiral type minizes the global cost Lt, i.e., the total length of connections (assuming that the points are distributed uniformly in the space), but has a low effciency in transporting flow from individual points to the outlet. On the contrary, the explosion type minizes the local cost Lt_bar (average lenth of the path from a point to the outlet), but consumes too many connections. Intuitively, the best stragegy two optimize both of the global and local cost is to generate a tree type illustrated by the third plot.  

Rinaldo et al. quantified the optimization goal, energy dissipation, in a very smart way. They define 

$$
A_i= \sum_j Aj + 1 \,\,\,\,\,   (1)
$$

as the area associated with the ith link on a spanning, loopless tree. The sum is over each upstream link j that inputs into i. As in the tree each link is associated with a unique site, Ai can also be viewed as the total number of sites "supporting" site i, like the number of employees under a head i within a company.

In Rinaldo et al.'s model, they assume that the potential engery dissipated in the ith link is

$$
E_i= k_i A_i A_i^{\gamma - 1}, \,\,\,\,\,   (2)
$$

i.e., the production between water flow in the landscape (which is assumed to be proportional to the area Ai) and the elevation difference along link i (which is assumed to scale with Ai with an exponent gamma - 1 empirically). Ki is a quantity related to the property of soil such as erodibility. In homogeneous models we usually assume ki is independent of i and equal to 1. 

The river networks will evolve to minimize the total energy dissipated, which is defined as 

$$
E= \sum_i A_i^ \gamma, \,\,\,\,\,(0<\gamma<1)  \,\,\,\,\,  (3)
$$

OCN suggested that the beatiful branching patterns we see in nature is acturally controlled by Eq.(3). In other word, we can say that flow networks have a common purpose to minimize Eq.(3), which is the driven force behind their evolution.

##Optimal channel networks in details 

![ocndemo](/media/files/2014-05-28-Flow-networks-have-a-purpose/ocndemo.png)
<sub>A demo of OCN. The area Ai is shown next to the corresponding site i and the total energy of the system is shown in the upper right corner.</sub>

Typically, OCN is defined as a tree on a 2D lattice of linear size L with only one global outlet. Each site on the lattice can only connect to one of its eight neighors. The above figure shows a lattice with L = 3 (left), an example random spanning tree obtained by [Prim's_algorithm] (http://en.wikipedia.org/wiki/Prim's_algorithm)(middle), and a rewired tree. A naive version of OCN is to use [Greedy algorithm] (http://en.wikipedia.org/wiki/Greedy_algorithm), i.e., generate a random tree and then randomly rewire the links of a node to see if the rewiring decreses the total enenergy E. 
