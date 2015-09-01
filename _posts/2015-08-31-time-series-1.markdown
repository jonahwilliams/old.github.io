---
layout: post
title:  "The Bar Plot"
date:   2015-09-01 23:17:22
categories: jekyll update
---

If you've ever clicked random buttons in excel, you've probably
made a bar plot accidentally at least once in your life.  A staple of business analysis,
The bar plot is also seen frequently in quarterly reports. Unlike other designs,
it is much easier to determine the exact numerical value for each point.
Click on the graph below to continue!



<div id="bar-plot"></div>
{::options parse_block_html="true" /}




Consider Tufte's Data-Ink Ratio:

>A large share of ink on a graphic should present data-information, the ink changing as the data change. Data-ink is the non-erasable core of a graphic, the non-redundant ink arranged in response to variation in the numbers represented.
-Tufte, 1983

Despite each data point being represented by a solid rectangle, the only relevant
information is mapped to the height.  We've used a whole lot of ink when just a little
line would do, because the width of the rectangle was not used to encode any data. Unlike in a histogram, where the width has a statistical interpretation as a range of aggregation, most bar plots use color and width arbitrarily.

Does the solid color rectangle enhance legibility?  Yes, however it also exposes another
flaw in the bar graph: Do we need a graph at all?  If the goal is legibility and
the number of data points are few, why not simply list the numbers?

7, 5, 2, 6, 10

<style>

.rectangle {
	fill: steelblue;
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
</style>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.6/d3.min.js"></script>
<script>
  (function () {
    var data = [
    {'x':1,'y':7}, {'x':2,'y':5}, {'x':3,'y':2},
    {'x':4,'y':6}, {'x':5, 'y':10}
    ];
    var state = 0;

    var margin = {top: 40, right: 40, bottom: 80, left: 40};
    var width = 600 - margin.left - margin.right;
    var height = 500 - margin.top - margin.bottom;

    var svg = d3.select("div#bar-plot").append("svg")
  	  .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
  	  .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
      .on('click', function (d) {
          if (state == 0) {
            state = 1;
            var rects = d3.selectAll(".rectangle")
              .transition()
              .duration(2000)
              .style("fill", "white")
              .style("stroke","black")
              .style("stroke-width","1px")
              .transition()
              .duration(2000)
              .attr("width", function (d) {
                  return 1;
              })
							.attr("x", function (d) {
									return x(d.x) + 0.5 * x.rangeBand();
							});
            }
        });

    var y = d3.scale.linear()
  		.domain([0, d3.max(data, function (d) { return d.y; })])
  		.range([height, 0]);

  	var x = d3.scale.ordinal()
  			.domain(data.map(function (d) { return d.x; }))
  			.rangeRoundBands([0, width], 0.5);

    var xAxis = d3.svg.axis()
    		.scale(x)
    	  .orient("bottom");

    var yAxis = d3.svg.axis()
    		.scale(y)
    	  .orient("left");

    svg.append("g")
      	.attr("class", "x axis")
      	.attr("transform", "translate(0," + height + ")")
      	.call(xAxis)
    svg.append("g")
      	.attr("class", "y axis")
      	.call(yAxis);

    svg.selectAll("rectangle")
      	.data(data)
      	.enter()
      	.append("rect")
      	.attr("class","rectangle")
      	.attr("width", x.rangeBand())
      	.attr("height", function (d) {
      			return height - y(d.y);
      	})
      	.attr("x", function (d) {return x(d.x);})
      	.attr("y", function (d) {return y(d.y);});

    })();
</script>
