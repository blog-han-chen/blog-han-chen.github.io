---
layout: post
title: "mining machine learning papers"
description: ""
category: machine-learning
tags: [machine-learning]
---
{% include JB/setup %}
{:center: style="text-align: center"}

Undergrads entering academia might be lost in finding where to start. First, they might want to know areas in researching are popular so that they can work on related problems to increase the chance of publication and citation. Second, they might be wondering which papers to read to most effectively understand the field. To answer those questions, I mined the publication data from (Microsoft Academic Graph)[https://academicgraph.blob.core.windows.net/graph-2016-02-05/index.html] and explored around. 

My primary focus is in the machine learning field. And I used (NEO4J)[http://neo4j.com/], to extract most of the patterns.  

What are the most often cited papers by new publications? 
---

<div id="graph1"></div>
<style>

.bar {
  fill: steelblue;
}

.bar:hover {
  fill: brown;
}

.axis {
  font: 10px sans-serif;
}

.axis path,
.axis line {
  fill: none;
  stroke: #000;
  shape-rendering: crispEdges;
}

.x.axis path {
  display: none;
}

</style>

<script src="//d3js.org/d3.v3.min.js"></script>
<script>

var margin = {top: 20, right: 20, bottom: 30, left: 40},
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;

var x = d3.scale.ordinal()
    .rangeRoundBands([0, width], .1);

var y = d3.scale.linear()
    .range([height, 0]);

var xAxis = d3.svg.axis()
    .scale(x)
    .orient("bottom");

var yAxis = d3.svg.axis()
    .scale(y)
    .orient("left")
    .ticks(10, "%");

var svg = d3.select("#graph1").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

d3.csv("/data/paper-citation.csv", type, function(error, data) {
  if (error) throw error;
  data.forEach(function(d){
    d.c = JSON.parse(d.c);
  });
  x.domain(data.map(function(d) { return d.c.title; }));
  y.domain([0, d3.max(data, function(d) { return d.connections; })]);

  svg.append("g")
      .attr("class", "x axis")
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis);

  svg.append("g")
      .attr("class", "y axis")
      .call(yAxis)
    .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 6)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text("citation");

  svg.selectAll(".bar")
      .data(data)
    .enter().append("rect")
      .attr("class", "bar")
      .attr("x", function(d) { return x(d.c.title); })
      .attr("width", x.rangeBand())
      .attr("y", function(d) { return y(d.connections); })
      .attr("height", function(d) { return height - y(d.connections); });
});

function type(d) {
  d.connections = +d.connections;
  return d;
}

</script>



