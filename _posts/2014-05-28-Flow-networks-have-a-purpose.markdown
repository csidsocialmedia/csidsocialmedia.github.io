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


###Why natural flow networks are so beautiful

The above figures visualize the U.S. river networks using [NHDPlus dataset](http://www.horizon-systems.com/nhdplus/) and [D3.js](http://www.somebits.com/rivers/rivers-d3.html). The amazing beauty of these natural flow systems motivates us to 
ask such questions: why they are so beautiful? Are there universal laws behind these systems, like the law of gravity ?
Or, is there a general "purpose" of these systems, like the principle of [least action](http://en.wikipedia.org/wiki/Principle_of_least_action) ?

[Rinaldo](http://www.image.unipd.it/a.rinaldo/allegati/Minimum_energy.pdf) (1992, 1993) et al. proposed a model called "optimal channel networks" (OCN) to explain the beutiful tree structure of flow networks. A comprehensive literature review on OCN can be found [here](http://abouthydrology.blogspot.com/2012/09/my-past-research-on-evolution-of-river.html). And [this](http://www.pnas.org/content/110/48/19295.abstract) and [this](http://www.pnas.org/content/early/2014/01/31/1322700111) are two recently pulished papers on OCN. 

<img src="/media/files/2014-05-28-Flow-networks-have-a-purpose/3flownetworks.png" height="500px" width="350px" />
<sub>(In the second plot there are two versions of global cost, depending on whether we put the futher points on the same line as the closer points.)</sub>

The above figure is cited from [here](http://onlinelibrary.wiley.com/doi/10.1029/91WR03034/abstract). The authors using this demo to 
explain the formation of river trees. They suggest a flow network has to minimize both of the global and local cost (in terms of flow dissipation, or energy dissipation cosidering potential energy, slope of basin, and many other factors). The spiral type minizes the global cost Lt, i.e., the total length of connections (assuming that the points are distributed uniformly in the space), but has a low effciency in transporting flow from individual points to the outlet. On the contrary, the explosion type minizes the local cost Lt_bar (average lenth of the path from a point to the outlet), but consumes too many connections. Intuitively, the best stragegy two optimize both of the global and local cost is to generate a tree type illustrated by the third plot.  
