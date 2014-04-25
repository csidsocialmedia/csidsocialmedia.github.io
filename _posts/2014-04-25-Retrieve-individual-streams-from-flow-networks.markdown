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


+ 1. user: 26624, time: 03:40:31, Question ID: 1983425
+ 2. user: 89771, time: 22:53:24, Question ID: 1989888
+ 3. user: 57348, time: 04:47:02, Question ID: 1987883
+ 4. user: 14343, time: 18:42:11, Question ID: 1989251
+ 5. user: 20489, time: 23:43:40, Question ID: 1987833
+ 6. user: 24587, time: 11:18:05, Question ID: 1988359
+ 7. user: 8206, time: 07:42:38, Question ID: 1988127
+ 8. user: 8206, time: 07:48:49, Question ID: 1988091
+ 9. user: 8206, time: 08:13:45, Question ID: 1988160
+ 10. user: 8206, time: 08:45:35, Question ID: 1988196
+ 11. user: 8206, time: 09:42:33, Question ID: 1988248
+ 12. user: 8206, time: 13:54:18, Question ID: 1988642
+ 13. user: 8206, time: 14:20:06, Question ID: 1988665
+ 14. user: 8206, time: 14:34:48, Question ID: 1988728
+ 15. user: 35501, time: 08:00:43, Question ID: 1988049
+ 16. user: 165905, time: 19:23:57, Question ID: 1989255
+ 17. user: 79891, time: 12:23:36, Question ID: 1980082


To study a flow network we shoud make sure it satistifies "flow equivalence" condition. That is, we add to artificial nodes, "soruce" and "sink", to balance the network such that inflow (the sum of weighted indegree) equals outflow (the sum of weighted indegree) on every node within the network. 

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

var width = 500,
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




