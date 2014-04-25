---
title: Retrieve individual streams from flow networks
layout: post
guid:
comments: true
tags:
  - network
  - PCI group
---

1. A flow network

The topology of real-world networks have been extensively studied, but the flow structure of networks has not attracted enough attention it deserves. In discussing flow structure we care about the directed, long-distance interactions between nodes and communities, which have strong applied meanings but are rarely covered by current network studies. 

The following figure shows a flow network demo. This flow network is an aggrenation of several streams. A stream is generated when a users of stackoverflow.com answer a series of questions sequentially, so the flow structure shows the interaction between users and questions. The above figure is contructre from the following data set:

    user: 26624, time: 03:40:31, Question ID: 1983425
    user: 89771, time: 22:53:24, Question ID: 1989888
    user: 57348, time: 04:47:02, Question ID: 1987883
    user: 14343, time: 18:42:11, Question ID: 1989251
    user: 20489, time: 23:43:40, Question ID: 1987833
    user: 24587, time: 11:18:05, Question ID: 1988359
    user: 8206, time: 07:42:38, Question ID: 1988127
    user: 8206, time: 07:48:49, Question ID: 1988091
    user: 8206, time: 08:13:45, Question ID: 1988160
    user: 8206, time: 08:45:35, Question ID: 1988196
    user: 8206, time: 09:42:33, Question ID: 1988248
    user: 8206, time: 13:54:18, Question ID: 1988642
    user: 8206, time: 14:20:06, Question ID: 1988665
    user: 8206, time: 14:34:48, Question ID: 1988728
    user: 35501, time: 08:00:43, Question ID: 1988049
    user: 165905, time: 19:23:57, Question ID: 1989255
    user: 79891, time: 12:23:36, Question ID: 1980082

To study a flow network we shoud make sure it satistifies "flow equivalence" condition. As shown in the attached figure, we add to artificial nodes, "soruce" and "sink", to balance the network such that inflow (the sum of weighted indegree) equals outflow (the sum of weighted indegree) on every node within the network. 

To construct and balance a flow network from individual records (the time variable should be removed first) we use the following python scripts:

    import networkx as nx
    import re
    from numpy import linalg as LA
    import numpy as np
    from collections import defaultdict
    from os import listdir
    from os.path import isfile, join
    import matplotlib.pyplot as plt
	
    def constructFlowNetwork (C):
        E=defaultdict(lambda:0)
        E[('source',C[0][1])]+=1
        E[(C[-1][1],'sink')]+=1
        F=zip(C[:-1],C[1:])
        for i in F:
            if i[0][0]==i[1][0]:
                E[(i[0][1],i[1][1])]+=1
            else:
                E[(i[0][1],'sink')]+=1
                E[('source',i[1][1])]+=1
        G=nx.DiGraph()
        for i,j in E.items():
            x,y=i
            G.add_edge(x,y,weight=j)
        return G
    
    def flowBalancing(G):
        RG = G.reverse()
        H = G.copy()
        def nodeBalancing(node):
            outw=0
            for i in G.edges(node):
                outw+=G[i[0]][i[1]].values()[0]
            inw=0
            for i in RG.edges(node):
                inw+=RG[i[0]][i[1]].values()[0]
            deltaflow=inw-outw
            if deltaflow > 0:
                H.add_edge(node, "sink",weight=deltaflow)
            elif deltaflow < 0:
                H.add_edge("source", node, weight=abs(deltaflow))
            else:
                pass
        for i in G.nodes():
            nodeBalancing(i)
        if ("source", "source") in H.edges():  H.remove_edge("source", "source")    
        if ("sink", "sink") in H.edges(): H.remove_edge("sink", "sink")
        return H
    
    def outflow(G,node):
        n=0
        for i in G.edges(node):
            n+=G[i[0]][i[1]].values()[0]
        return n
    

By applying the above scripts we obtain the weighted, directed flow network

    1988160->1988196: 1
    1983425->sink: 1
    1988642->1988665: 1
    1989251->sink: 1
    1988196->1988248: 1
    1989255->sink: 1
    1988359->sink: 1
    1989888->sink: 1
    1988049->sink: 1
    source->1989888: 1
    source->1983425: 1
    source->1989251: 1
    source->1988359: 1
    source->1989255: 1
    source->1988049: 1
    source->1980082: 1
    source->1987883: 1
    source->1987833: 1
    source->1988127: 1
    1980082->sink: 1
    1987883->sink: 1
    1988728->sink: 1
    1988665->1988728: 1
    1988248->1988642: 1
    1987833->sink: 1
    1988091->1988160: 1
    1988127->1988091: 1

On this balanced flow network we can calculate the network inflow (which equals the network outflow) as 10 - this is also the number of individual streams (individual users), and the network total flow is 27. Dividing 27 by 10 is 2.7, this is the average flow length (the number of questions answered by an average user).

Now, a particularly interesting question is, given this flow network structure, can we retrive individual ctreams ? This idea sounds crazy, but it is not entirely impossible. I found that by using the [Dijkstra algorithm](http://en.wikipedia.org/wiki/Dijkstra's_algorithm) recursively it is possible to do this. I would not release my scripts here, but we can check the result:

    ('source', 1980082, 'sink'): 1,
    ('source', 1983425, 'sink'): 1,
    ('source', 1987833, 'sink'): 1,
    ('source', 1987883, 'sink'): 1,
    ('source', 1988049, 'sink'): 1,
    ('source', 1988127, 1988091, 1988160, 1988196, 1988248, 1988642, 1988665, 1988728, 'sink'): 1,
    ('source', 1988359, 'sink'): 1,
    ('source', 1989251, 'sink'): 1,
    ('source', 1989255, 'sink'): 1,
    ('source', 1989888, 'sink'): 1}
	

This is amzaing, we successively retrived every single path! But I also found that my scripts have two limitations. 1. It can not handle loops (this is the limitation of the Dijkstra algorithm itself); 2. Some times a (macro) flow network structure (such as two triangles heading at each other) is a result of several equivalent (micro) combinations. In these cases my scripts can not gurantee the retrieved individual streams are correct. 

For example, to construct flow from the following two data sets:

	E1 = [['user A',0],['user A',1],['user A',2],['user A',3],['user B',2]]
	E2 = [['user A',0],['user A',1],['user A',2],['user B',2],['user B',3]]

We all get 


    0->1: 1
    1->2: 1
    2->3: 1
    2->sink: 1
    3->sink: 1
    source->0: 1
    source->2: 1



<body>
<style>

.link {
  fill: none;
  stroke: #666;
  stroke-width: 1.5px;
}

#fromSource{
	fill:green;
}

.link.fromSource{
	stroke:green;
}

#toSink{
	fill:red;
}

.link.toSink{
	stroke:red;
}

circle {
  fill: #ccc;
  stroke: #333;
  stroke-width: 1.5px;
}

text {
  font: 10px sans-serif;
  pointer-events: none;
  text-shadow: 0 1px 0 #fff, 1px 0 0 #fff, 0 -1px 0 #fff, -1px 0 0 #fff;
}

</style>
<body>
<script src="http://d3js.org/d3.v3.min.js"></script>
<script>

var links = 

[{'source': 1988160, 'type': 'normal', 'target': 1988196}, {'source': 1983425, 'type': 'toSink', 'target': 'sink'}, {'source': 1988642, 'type': 'normal', 'target': 1988665}, {'source': 1989251, 'type': 'toSink', 'target': 'sink'}, {'source': 1988196, 'type': 'normal', 'target': 1988248}, {'source': 1989255, 'type': 'toSink', 'target': 'sink'}, {'source': 1988359, 'type': 'toSink', 'target': 'sink'}, {'source': 1989888, 'type': 'toSink', 'target': 'sink'}, {'source': 1988049, 'type': 'toSink', 'target': 'sink'}, {'source': 'source', 'type': 'fromSource', 'target': 1989888}, {'source': 'source', 'type': 'fromSource', 'target': 1983425}, {'source': 'source', 'type': 'fromSource', 'target': 1989251}, {'source': 'source', 'type': 'fromSource', 'target': 1988359}, {'source': 'source', 'type': 'fromSource', 'target': 1989255}, {'source': 'source', 'type': 'fromSource', 'target': 1988049}, {'source': 'source', 'type': 'fromSource', 'target': 1980082}, {'source': 'source', 'type': 'fromSource', 'target': 1987883}, {'source': 'source', 'type': 'fromSource', 'target': 1987833}, {'source': 'source', 'type': 'fromSource', 'target': 1988127}, {'source': 1980082, 'type': 'toSink', 'target': 'sink'}, {'source': 1987883, 'type': 'toSink', 'target': 'sink'}, {'source': 1988728, 'type': 'toSink', 'target': 'sink'}, {'source': 1988665, 'type': 'normal', 'target': 1988728}, {'source': 1988248, 'type': 'normal', 'target': 1988642}, {'source': 1987833, 'type': 'toSink', 'target': 'sink'}, {'source': 1988091, 'type': 'normal', 'target': 1988160}, {'source': 1988127, 'type': 'normal', 'target': 1988091}]

;

var nodes = {};

// Compute the distinct nodes from the links.
links.forEach(function(link) {
  link.source = nodes[link.source] || (nodes[link.source] = {name: link.source});
  link.target = nodes[link.target] || (nodes[link.target] = {name: link.target});
});

var width = 960,
    height = 500;

var force = d3.layout.force()
    .nodes(d3.values(nodes))
    .links(links)
    .size([width, height])
    .linkDistance(60)
    .charge(-300)
    .on("tick", tick)
    .start();

var svg = d3.select("body").append("svg")
    .attr("width", width)
    .attr("height", height);

// Per-type markers, as they don't inherit styles.
svg.append("defs").selectAll("marker")
    .data(["normal","fromSource","toSink"])
  .enter().append("marker")
    .attr("id", function(d) { return d; })
    .attr("viewBox", "0 -5 10 10")
    .attr("refX", 15)
    .attr("refY", -1.5)
    .attr("markerWidth", 6)
    .attr("markerHeight", 6)
    .attr("orient", "auto")
  .append("path")
    .attr("d", "M0,-5L10,0L0,5");

var path = svg.append("g").selectAll("path")
    .data(force.links())
  .enter().append("path")
    .attr("class", function(d) { return "link "+ d.type; })
	.attr("marker-end", function(d) { return "url(#" + d.type + ")"; });
	

var circle = svg.append("g").selectAll("circle")
    .data(force.nodes())
  .enter().append("circle")
    .attr("r", 6)
    .call(force.drag);

var text = svg.append("g").selectAll("text")
    .data(force.nodes())
  .enter().append("text")
    .attr("x", 8)
    .attr("y", ".31em")
    .text(function(d) { return d.name; });

// Use elliptical arc path segments to doubly-encode directionality.
function tick() {
  path.attr("d", linkArc);
  circle.attr("transform", transform);
  text.attr("transform", transform);
}

function linkArc(d) {
  var dx = d.target.x - d.source.x,
      dy = d.target.y - d.source.y,
      dr = Math.sqrt(dx * dx + dy * dy);
  return "M" + d.source.x + "," + d.source.y + "A" + dr + "," + dr + " 0 0,1 " + d.target.x + "," + d.target.y;
}

function transform(d) {
  return "translate(" + d.x + "," + d.y + ")";
}

</script>
	
</body>




