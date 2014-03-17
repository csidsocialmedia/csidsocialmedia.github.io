---
title: Network visualization by D3
layout: post
guid: 
tags:
  - network
  - visualization
  - D3
---

This is an experiment of network visualization. I used [d3Network](http://christophergandrud.github.io/d3Network/) in R to generate this dynamic network plot. The used data set contains sequential tagging behaviors on Delicious on 2003-01-1. The data is downloaded from [PINTS](http://www.uni-koblenz-landau.de/koblenz/fb4/AGStaab/Research/DataSets/PINTSExperimentsDataSets/index_html) project. 

	
 <style> 
.link {  
stroke: #666;
opacity: 0.9;
stroke-width: 1.5px; 
} 
.node circle { 
stroke: #fff; 
opacity: 0.9;
stroke-width: 1.5px; 
} 
.node:not(:hover) .nodetext {
display: none;
}
text { 
font: 7px serif; 
opacity: 0.9;
pointer-events: none; 
} 
</style> 

<script src=http://d3js.org/d3.v3.min.js></script>

<script> 
 var links = [ { "source" : 0, "target" : 0, "value" : 5 }, { "source" : 0, "target" : 4, "value" : 1 }, { "source" : 0, "target" : 8, "value" : 1 }, { "source" : 0, "target" : 10, "value" : 1 }, { "source" : 0, "target" : 11, "value" : 3 }, { "source" : 0, "target" : 12, "value" : 1 }, { "source" : 0, "target" : 17, "value" : 2 }, { "source" : 0, "target" : 19, "value" : 1 }, { "source" : 0, "target" : 22, "value" : 1 }, { "source" : 0, "target" : 23, "value" : 1 }, { "source" : 0, "target" : 26, "value" : 1 }, { "source" : 0, "target" : 30, "value" : 3 }, { "source" : 0, "target" : 34, "value" : 2 }, { "source" : 0, "target" : 36, "value" : 1 }, { "source" : 0, "target" : 44, "value" : 1 }, { "source" : 0, "target" : 45, "value" : 4 }, { "source" : 0, "target" : 46, "value" : 4 }, { "source" : 0, "target" : 47, "value" : 1 }, { "source" : 0, "target" : 48, "value" : 2 }, { "source" : 0, "target" : 50, "value" : 2 }, { "source" : 0, "target" : 56, "value" : 5 }, { "source" : 1, "target" : 11, "value" : 1 }, { "source" : 1, "target" : 36, "value" : 1 }, { "source" : 1, "target" : 45, "value" : 1 }, { "source" : 2, "target" : 30, "value" : 1 }, { "source" : 2, "target" : 44, "value" : 1 }, { "source" : 2, "target" : 45, "value" : 2 }, { "source" : 3, "target" : 58, "value" : 1 }, { "source" : 4, "target" : 0, "value" : 2 }, { "source" : 4, "target" : 10, "value" : 2 }, { "source" : 4, "target" : 12, "value" : 1 }, { "source" : 4, "target" : 25, "value" : 1 }, { "source" : 4, "target" : 46, "value" : 1 }, { "source" : 4, "target" : 59, "value" : 1 }, { "source" : 5, "target" : 34, "value" : 1 }, { "source" : 5, "target" : 44, "value" : 1 }, { "source" : 6, "target" : 11, "value" : 2 }, { "source" : 6, "target" : 19, "value" : 3 }, { "source" : 6, "target" : 23, "value" : 2 }, { "source" : 6, "target" : 46, "value" : 1 }, { "source" : 6, "target" : 48, "value" : 1 }, { "source" : 6, "target" : 56, "value" : 1 }, { "source" : 7, "target" : 0, "value" : 1 }, { "source" : 7, "target" : 9, "value" : 1 }, { "source" : 7, "target" : 30, "value" : 1 }, { "source" : 7, "target" : 45, "value" : 1 }, { "source" : 8, "target" : 7, "value" : 1 }, { "source" : 8, "target" : 14, "value" : 1 }, { "source" : 8, "target" : 56, "value" : 2 }, { "source" : 9, "target" : 0, "value" : 1 }, { "source" : 9, "target" : 8, "value" : 1 }, { "source" : 9, "target" : 30, "value" : 1 }, { "source" : 9, "target" : 44, "value" : 2 }, { "source" : 9, "target" : 45, "value" : 2 }, { "source" : 9, "target" : 56, "value" : 1 }, { "source" : 10, "target" : 0, "value" : 2 }, { "source" : 10, "target" : 4, "value" : 1 }, { "source" : 10, "target" : 9, "value" : 1 }, { "source" : 10, "target" : 11, "value" : 1 }, { "source" : 10, "target" : 17, "value" : 2 }, { "source" : 10, "target" : 23, "value" : 1 }, { "source" : 10, "target" : 44, "value" : 3 }, { "source" : 10, "target" : 45, "value" : 5 }, { "source" : 10, "target" : 46, "value" : 1 }, { "source" : 10, "target" : 56, "value" : 3 }, { "source" : 11, "target" : 0, "value" : 2 }, { "source" : 11, "target" : 1, "value" : 1 }, { "source" : 11, "target" : 6, "value" : 1 }, { "source" : 11, "target" : 9, "value" : 1 }, { "source" : 11, "target" : 10, "value" : 2 }, { "source" : 11, "target" : 12, "value" : 1 }, { "source" : 11, "target" : 34, "value" : 2 }, { "source" : 11, "target" : 46, "value" : 3 }, { "source" : 11, "target" : 47, "value" : 1 }, { "source" : 12, "target" : 11, "value" : 1 }, { "source" : 12, "target" : 17, "value" : 1 }, { "source" : 12, "target" : 33, "value" : 1 }, { "source" : 12, "target" : 37, "value" : 1 }, { "source" : 12, "target" : 44, "value" : 1 }, { "source" : 12, "target" : 48, "value" : 1 }, { "source" : 12, "target" : 56, "value" : 1 }, { "source" : 13, "target" : 11, "value" : 1 }, { "source" : 13, "target" : 46, "value" : 1 }, { "source" : 13, "target" : 50, "value" : 1 }, { "source" : 14, "target" : 0, "value" : 1 }, { "source" : 14, "target" : 14, "value" : 1 }, { "source" : 14, "target" : 22, "value" : 1 }, { "source" : 14, "target" : 36, "value" : 2 }, { "source" : 14, "target" : 44, "value" : 1 }, { "source" : 14, "target" : 45, "value" : 2 }, { "source" : 14, "target" : 47, "value" : 1 }, { "source" : 14, "target" : 56, "value" : 1 }, { "source" : 15, "target" : 60, "value" : 1 }, { "source" : 16, "target" : 61, "value" : 1 }, { "source" : 17, "target" : 0, "value" : 3 }, { "source" : 17, "target" : 1, "value" : 1 }, { "source" : 17, "target" : 6, "value" : 1 }, { "source" : 17, "target" : 8, "value" : 1 }, { "source" : 17, "target" : 9, "value" : 1 }, { "source" : 17, "target" : 10, "value" : 2 }, { "source" : 17, "target" : 17, "value" : 2 }, { "source" : 17, "target" : 21, "value" : 1 }, { "source" : 17, "target" : 31, "value" : 1 }, { "source" : 17, "target" : 36, "value" : 2 }, { "source" : 17, "target" : 46, "value" : 1 }, { "source" : 17, "target" : 47, "value" : 1 }, { "source" : 17, "target" : 62, "value" : 1 }, { "source" : 17, "target" : 81, "value" : 0.01 }, { "source" : 18, "target" : 63, "value" : 1 }, { "source" : 19, "target" : 5, "value" : 1 }, { "source" : 19, "target" : 6, "value" : 1 }, { "source" : 19, "target" : 10, "value" : 3 }, { "source" : 19, "target" : 21, "value" : 1 }, { "source" : 19, "target" : 34, "value" : 1 }, { "source" : 19, "target" : 36, "value" : 1 }, { "source" : 19, "target" : 47, "value" : 5 }, { "source" : 20, "target" : 3, "value" : 1 }, { "source" : 21, "target" : 0, "value" : 1 }, { "source" : 21, "target" : 17, "value" : 1 }, { "source" : 21, "target" : 45, "value" : 2 }, { "source" : 21, "target" : 50, "value" : 1 }, { "source" : 22, "target" : 14, "value" : 1 }, { "source" : 22, "target" : 17, "value" : 1 }, { "source" : 22, "target" : 22, "value" : 1 }, { "source" : 22, "target" : 26, "value" : 1 }, { "source" : 22, "target" : 45, "value" : 1 }, { "source" : 23, "target" : 10, "value" : 1 }, { "source" : 23, "target" : 46, "value" : 1 }, { "source" : 23, "target" : 47, "value" : 2 }, { "source" : 24, "target" : 64, "value" : 1 }, { "source" : 25, "target" : 19, "value" : 1 }, { "source" : 25, "target" : 45, "value" : 1 }, { "source" : 25, "target" : 48, "value" : 1 }, { "source" : 25, "target" : 50, "value" : 2 }, { "source" : 26, "target" : 36, "value" : 1 }, { "source" : 26, "target" : 46, "value" : 1 }, { "source" : 27, "target" : 17, "value" : 1 }, { "source" : 28, "target" : 65, "value" : 1 }, { "source" : 29, "target" : 19, "value" : 1 }, { "source" : 30, "target" : 0, "value" : 3 }, { "source" : 30, "target" : 2, "value" : 1 }, { "source" : 30, "target" : 9, "value" : 1 }, { "source" : 30, "target" : 31, "value" : 1 }, { "source" : 30, "target" : 47, "value" : 3 }, { "source" : 31, "target" : 0, "value" : 1 }, { "source" : 31, "target" : 19, "value" : 1 }, { "source" : 31, "target" : 44, "value" : 1 }, { "source" : 31, "target" : 50, "value" : 1 }, { "source" : 32, "target" : 28, "value" : 1 }, { "source" : 33, "target" : 21, "value" : 1 }, { "source" : 33, "target" : 66, "value" : 1 }, { "source" : 34, "target" : 0, "value" : 1 }, { "source" : 34, "target" : 4, "value" : 2 }, { "source" : 34, "target" : 12, "value" : 1 }, { "source" : 34, "target" : 45, "value" : 2 }, { "source" : 34, "target" : 46, "value" : 1 }, { "source" : 34, "target" : 56, "value" : 1 }, { "source" : 35, "target" : 45, "value" : 1 }, { "source" : 36, "target" : 1, "value" : 1 }, { "source" : 36, "target" : 6, "value" : 1 }, { "source" : 36, "target" : 10, "value" : 1 }, { "source" : 36, "target" : 11, "value" : 1 }, { "source" : 36, "target" : 14, "value" : 1 }, { "source" : 36, "target" : 17, "value" : 1 }, { "source" : 36, "target" : 19, "value" : 1 }, { "source" : 36, "target" : 37, "value" : 1 }, { "source" : 36, "target" : 45, "value" : 2 }, { "source" : 36, "target" : 46, "value" : 1 }, { "source" : 36, "target" : 47, "value" : 1 }, { "source" : 36, "target" : 48, "value" : 1 }, { "source" : 36, "target" : 56, "value" : 2 }, { "source" : 37, "target" : 37, "value" : 1 }, { "source" : 37, "target" : 46, "value" : 2 }, { "source" : 38, "target" : 38, "value" : 2 }, { "source" : 38, "target" : 42, "value" : 1 }, { "source" : 38, "target" : 67, "value" : 1 }, { "source" : 38, "target" : 81, "value" : 0.04 }, { "source" : 39, "target" : 41, "value" : 1 }, { "source" : 39, "target" : 54, "value" : 1 }, { "source" : 40, "target" : 68, "value" : 1 }, { "source" : 41, "target" : 39, "value" : 1 }, { "source" : 42, "target" : 38, "value" : 2 }, { "source" : 42, "target" : 42, "value" : 1 }, { "source" : 43, "target" : 55, "value" : 1 }, { "source" : 44, "target" : 0, "value" : 5 }, { "source" : 44, "target" : 6, "value" : 1 }, { "source" : 44, "target" : 10, "value" : 3 }, { "source" : 44, "target" : 12, "value" : 1 }, { "source" : 44, "target" : 14, "value" : 1 }, { "source" : 44, "target" : 25, "value" : 2 }, { "source" : 44, "target" : 47, "value" : 1 }, { "source" : 44, "target" : 49, "value" : 1 }, { "source" : 45, "target" : 0, "value" : 2 }, { "source" : 45, "target" : 2, "value" : 2 }, { "source" : 45, "target" : 6, "value" : 3 }, { "source" : 45, "target" : 7, "value" : 1 }, { "source" : 45, "target" : 8, "value" : 1 }, { "source" : 45, "target" : 9, "value" : 1 }, { "source" : 45, "target" : 10, "value" : 2 }, { "source" : 45, "target" : 13, "value" : 1 }, { "source" : 45, "target" : 14, "value" : 1 }, { "source" : 45, "target" : 21, "value" : 2 }, { "source" : 45, "target" : 25, "value" : 1 }, { "source" : 45, "target" : 34, "value" : 1 }, { "source" : 45, "target" : 36, "value" : 1 }, { "source" : 45, "target" : 46, "value" : 4 }, { "source" : 45, "target" : 47, "value" : 7 }, { "source" : 45, "target" : 49, "value" : 1 }, { "source" : 46, "target" : 0, "value" : 5 }, { "source" : 46, "target" : 4, "value" : 3 }, { "source" : 46, "target" : 10, "value" : 1 }, { "source" : 46, "target" : 11, "value" : 3 }, { "source" : 46, "target" : 14, "value" : 1 }, { "source" : 46, "target" : 17, "value" : 1 }, { "source" : 46, "target" : 30, "value" : 1 }, { "source" : 46, "target" : 36, "value" : 2 }, { "source" : 46, "target" : 44, "value" : 1 }, { "source" : 46, "target" : 45, "value" : 1 }, { "source" : 46, "target" : 46, "value" : 3 }, { "source" : 46, "target" : 47, "value" : 4 }, { "source" : 46, "target" : 50, "value" : 3 }, { "source" : 46, "target" : 81, "value" : 0.01 }, { "source" : 47, "target" : 5, "value" : 1 }, { "source" : 47, "target" : 7, "value" : 1 }, { "source" : 47, "target" : 11, "value" : 1 }, { "source" : 47, "target" : 13, "value" : 1 }, { "source" : 47, "target" : 14, "value" : 1 }, { "source" : 47, "target" : 17, "value" : 6 }, { "source" : 47, "target" : 19, "value" : 5 }, { "source" : 47, "target" : 30, "value" : 2 }, { "source" : 47, "target" : 31, "value" : 1 }, { "source" : 47, "target" : 44, "value" : 2 }, { "source" : 47, "target" : 45, "value" : 3 }, { "source" : 47, "target" : 46, "value" : 2 }, { "source" : 47, "target" : 47, "value" : 1 }, { "source" : 47, "target" : 48, "value" : 3 }, { "source" : 47, "target" : 56, "value" : 5 }, { "source" : 48, "target" : 0, "value" : 2 }, { "source" : 48, "target" : 6, "value" : 1 }, { "source" : 48, "target" : 10, "value" : 1 }, { "source" : 48, "target" : 12, "value" : 1 }, { "source" : 48, "target" : 14, "value" : 1 }, { "source" : 48, "target" : 34, "value" : 1 }, { "source" : 48, "target" : 36, "value" : 1 }, { "source" : 48, "target" : 47, "value" : 1 }, { "source" : 49, "target" : 44, "value" : 1 }, { "source" : 49, "target" : 45, "value" : 1 }, { "source" : 50, "target" : 0, "value" : 5 }, { "source" : 50, "target" : 2, "value" : 1 }, { "source" : 50, "target" : 13, "value" : 1 }, { "source" : 50, "target" : 22, "value" : 1 }, { "source" : 50, "target" : 31, "value" : 1 }, { "source" : 50, "target" : 47, "value" : 1 }, { "source" : 50, "target" : 51, "value" : 1 }, { "source" : 50, "target" : 81, "value" : 0.01 }, { "source" : 51, "target" : 50, "value" : 1 }, { "source" : 52, "target" : 33, "value" : 1 }, { "source" : 52, "target" : 81, "value" : 0.01 }, { "source" : 53, "target" : 69, "value" : 1 }, { "source" : 54, "target" : 24, "value" : 1 }, { "source" : 55, "target" : 16, "value" : 1 }, { "source" : 56, "target" : 0, "value" : 1 }, { "source" : 56, "target" : 6, "value" : 1 }, { "source" : 56, "target" : 7, "value" : 1 }, { "source" : 56, "target" : 9, "value" : 2 }, { "source" : 56, "target" : 10, "value" : 1 }, { "source" : 56, "target" : 12, "value" : 1 }, { "source" : 56, "target" : 14, "value" : 1 }, { "source" : 56, "target" : 22, "value" : 1 }, { "source" : 56, "target" : 25, "value" : 1 }, { "source" : 56, "target" : 29, "value" : 1 }, { "source" : 56, "target" : 35, "value" : 1 }, { "source" : 56, "target" : 36, "value" : 3 }, { "source" : 56, "target" : 46, "value" : 2 }, { "source" : 56, "target" : 47, "value" : 5 }, { "source" : 57, "target" : 70, "value" : 1 }, { "source" : 58, "target" : 81, "value" : 0.01 }, { "source" : 59, "target" : 81, "value" : 0.01 }, { "source" : 60, "target" : 81, "value" : 0.01 }, { "source" : 61, "target" : 81, "value" : 0.01 }, { "source" : 62, "target" : 81, "value" : 0.01 }, { "source" : 63, "target" : 81, "value" : 0.01 }, { "source" : 64, "target" : 81, "value" : 0.01 }, { "source" : 65, "target" : 81, "value" : 0.01 }, { "source" : 66, "target" : 81, "value" : 0.01 }, { "source" : 67, "target" : 81, "value" : 0.01 }, { "source" : 68, "target" : 81, "value" : 0.01 }, { "source" : 69, "target" : 81, "value" : 0.01 }, { "source" : 70, "target" : 81, "value" : 0.01 }, { "source" : 71, "target" : 81, "value" : 0.01 }, { "source" : 72, "target" : 81, "value" : 0.01 }, { "source" : 73, "target" : 81, "value" : 0.01 }, { "source" : 74, "target" : 81, "value" : 0.01 }, { "source" : 75, "target" : 81, "value" : 0.01 }, { "source" : 76, "target" : 81, "value" : 0.01 }, { "source" : 77, "target" : 81, "value" : 0.01 }, { "source" : 78, "target" : 81, "value" : 0.01 }, { "source" : 79, "target" : 81, "value" : 0.01 }, { "source" : 80, "target" : 4, "value" : 0.01 }, { "source" : 80, "target" : 15, "value" : 0.01 }, { "source" : 80, "target" : 17, "value" : 0.01 }, { "source" : 80, "target" : 18, "value" : 0.01 }, { "source" : 80, "target" : 20, "value" : 0.01 }, { "source" : 80, "target" : 27, "value" : 0.01 }, { "source" : 80, "target" : 32, "value" : 0.01 }, { "source" : 80, "target" : 38, "value" : 0.04 }, { "source" : 80, "target" : 39, "value" : 0.01 }, { "source" : 80, "target" : 40, "value" : 0.01 }, { "source" : 80, "target" : 42, "value" : 0.01 }, { "source" : 80, "target" : 43, "value" : 0.01 }, { "source" : 80, "target" : 46, "value" : 0.01 }, { "source" : 80, "target" : 50, "value" : 0.01 }, { "source" : 80, "target" : 52, "value" : 0.02 }, { "source" : 80, "target" : 53, "value" : 0.01 }, { "source" : 80, "target" : 57, "value" : 0.01 }, { "source" : 80, "target" : 71, "value" : 0.01 }, { "source" : 80, "target" : 72, "value" : 0.01 }, { "source" : 80, "target" : 73, "value" : 0.01 }, { "source" : 80, "target" : 74, "value" : 0.01 }, { "source" : 80, "target" : 75, "value" : 0.01 }, { "source" : 80, "target" : 76, "value" : 0.01 }, { "source" : 80, "target" : 77, "value" : 0.01 }, { "source" : 80, "target" : 78, "value" : 0.01 }, { "source" : 80, "target" : 79, "value" : 0.01 } ] ; 
 var nodes = [ { "name" : "shop", "group" : 2 }, { "name" : "feed", "group" : 2 }, { "name" : "fonts", "group" : 2 }, { "name" : "cisco", "group" : 3 }, { "name" : "reference", "group" : 2 }, { "name" : "photoshop", "group" : 2 }, { "name" : "win", "group" : 2 }, { "name" : "robotics", "group" : 2 }, { "name" : "share", "group" : 2 }, { "name" : "themes", "group" : 2 }, { "name" : "woodworking", "group" : 2 }, { "name" : "video", "group" : 2 }, { "name" : "rpg", "group" : 2 }, { "name" : "toys", "group" : 2 }, { "name" : "home", "group" : 2 }, { "name" : "gcss", "group" : 4 }, { "name" : "xp", "group" : 5 }, { "name" : "web", "group" : 2 }, { "name" : "importados", "group" : 6 }, { "name" : "java", "group" : 2 }, { "name" : "americana", "group" : 3 }, { "name" : "service", "group" : 2 }, { "name" : "script", "group" : 2 }, { "name" : "humor", "group" : 2 }, { "name" : "entertainment", "group" : 7 }, { "name" : "how", "group" : 2 }, { "name" : "health", "group" : 2 }, { "name" : "usenet", "group" : 2 }, { "name" : "genealogy", "group" : 8 }, { "name" : "money", "group" : 2 }, { "name" : "music", "group" : 2 }, { "name" : "electronics", "group" : 2 }, { "name" : "gedcom", "group" : 8 }, { "name" : "literature", "group" : 2 }, { "name" : "flight", "group" : 2 }, { "name" : "essay", "group" : 2 }, { "name" : "food", "group" : 2 }, { "name" : "government", "group" : 2 }, { "name" : "imported", "group" : 9 }, { "name" : "movies", "group" : 7 }, { "name" : "enciclopedia", "group" : 10 }, { "name" : "release", "group" : 7 }, { "name" : "mac", "group" : 9 }, { "name" : "dossierdelabarrepersonnelle", "group" : 5 }, { "name" : "photography", "group" : 2 }, { "name" : "software", "group" : 2 }, { "name" : "hardware", "group" : 2 }, { "name" : "circuits", "group" : 2 }, { "name" : "science", "group" : 2 }, { "name" : "icons", "group" : 2 }, { "name" : "programming", "group" : 2 }, { "name" : "career", "group" : 2 }, { "name" : "books", "group" : 2 }, { "name" : "freebsd", "group" : 11 }, { "name" : "dvd", "group" : 7 }, { "name" : "guru", "group" : 5 }, { "name" : "design", "group" : 2 }, { "name" : "clothes", "group" : 12 }, { "name" : "admin", "group" : 3 }, { "name" : "language", "group" : 2 }, { "name" : "show", "group" : 4 }, { "name" : "cvs", "group" : 5 }, { "name" : "usabilidad", "group" : 2 }, { "name" : "dise", "group" : 6 }, { "name" : "schedule", "group" : 7 }, { "name" : "xml", "group" : 8 }, { "name" : "tolkien", "group" : 2 }, { "name" : "activism", "group" : 9 }, { "name" : "informacion", "group" : 10 }, { "name" : "bsd", "group" : 11 }, { "name" : "shopping", "group" : 12 }, { "name" : "being", "group" : 12 }, { "name" : "library", "group" : 12 }, { "name" : "mozilla", "group" : 12 }, { "name" : "p", "group" : 12 }, { "name" : "hamburg", "group" : 12 }, { "name" : "linux", "group" : 12 }, { "name" : "homepages", "group" : 12 }, { "name" : "computers", "group" : 12 }, { "name" : "shareware", "group" : 12 }, { "name" : "source", "group" : 0 }, { "name" : "sink", "group" : 1 } ] ; 
 var width = 800
height = 800;

var color = d3.scale.category20();

var force = d3.layout.force()
.nodes(d3.values(nodes)) 
.links(links) 
.size([width, height]) 
.linkDistance(50) 
.charge(-50) 
.on("tick", tick) 
.start(); 

var svg = d3.select("body").append("svg")
.attr("width", width)
.attr("height", height);

var link = svg.selectAll(".link")
.data(force.links())
.enter().append("line")
.attr("class", "link")
.style("stroke-width", function(d) { return Math.sqrt(d.value); });

var node = svg.selectAll(".node")
.data(force.nodes())
.enter().append("g") 
.attr("class", "node")
.style("fill", function(d) { return color(d.group); })
.style("opacity", 0.9)
.on("mouseover", mouseover) 
.on("mouseout", mouseout) 
.call(force.drag);

node.append("circle") 
.attr("r", 6)

node.append("svg:text")
.attr("class", "nodetext")
.attr("dx", 12)
.attr("dy", ".35em")
.text(function(d) { return d.name });

function tick() { 
link 
.attr("x1", function(d) { return d.source.x; }) 
.attr("y1", function(d) { return d.source.y; }) 
.attr("x2", function(d) { return d.target.x; }) 
.attr("y2", function(d) { return d.target.y; }); 

node.attr("transform", function(d) { return "translate(" + d.x + "," + d.y + ")"; }); 
} 

function mouseover() { 
d3.select(this).select("circle").transition() 
.duration(750) 
.attr("r", 16);
d3.select(this).select("text").transition()
.duration(750)
.attr("x", 13)
.style("stroke-width", ".5px")
.style("font", "17.5px serif")
.style("opacity", 1); 
} 

function mouseout() { 
d3.select(this).select("circle").transition() 
.duration(750) 
.attr("r", 8); 
} 

</script>
