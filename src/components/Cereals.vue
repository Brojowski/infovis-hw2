<template>
  <div>
    <h1>Cereals</h1>

    <svg id="vis2">
    </svg>

    <div id="focusbox">
    </div>

    <h2> Description </h2>
    <p>
    </p>
  </div>
</template>

<script>
/* eslint no-unused-vars: "off" */
import * as d3 from 'd3';

export default {
  name: 'Cereals',
  mounted: function() {
        // https://bl.ocks.org/jasondavies/1341281

        var margin = {top: 30, right: 10, bottom: 10, left: 10},
            width = 960 - margin.left - margin.right,
            height = 500 - margin.top - margin.bottom;
        
        var x = d3.scalePoint()
                .range([0, width])
                .padding(.1),
            y = {},
            dragging = {};
        
        var line = d3.line(),
            axis = d3.axisLeft(),
            background,
            foreground;

        var dimensions = [];
        
        var svg = d3.select("#vis2")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
          .append("g")
            .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
        
        function renderVis(cars) {
          // Extract the list of dimensions and create a scale for each.
          dimensions = d3.keys(cars[0]).filter(function(d) {
            return d != "cereal" 
                && d != "manufacturer"
                && d != "type"
                && (y[d] = d3.scaleLinear()
                    .domain(d3.extent(cars, function(p) { return Number.parseFloat(p[d]); }))
                    .range([height, 0]));
          })

           x.domain(dimensions);

          // Add grey background lines for context.
          background = svg.append("g")
              .attr("class", "background")
            .selectAll("path")
              .data(cars)
            .enter().append("path")
              .attr("fill", "none")
              .attr("stroke", "steelblue")
              .attr("d", path);
        
          // Add blue foreground lines for focus.
          foreground = svg.append("g")
              .attr("class", "foreground")
            .selectAll("path")
              .data(cars)
            .enter().append("path")
              .attr("fill", "none")
              .attr("stroke", "steelblue")
              .attr("d", path);
        
          // Add a group element for each dimension.
          var g = svg.selectAll(".dimension")
              .data(dimensions)
            .enter().append("g")
              .attr("class", "dimension")
              .attr("transform", function(d) { return "translate(" + x(d) + ")"; })
            //  .drag()
            //    .origin(function(d) { return {x: x(d)}; })
            //    .on("dragstart", function(d) {
            //      dragging[d] = x(d);
            //      background.attr("visibility", "hidden");
            //    })
            //    .on("drag", function(d) {
            //      dragging[d] = Math.min(width, Math.max(0, d3.event.x));
            //      foreground.attr("d", path);
            //      dimensions.sort(function(a, b) { return position(a) - position(b); });
            //      x.domain(dimensions);
            //      g.attr("transform", function(d) { return "translate(" + position(d) + ")"; })
            //    })
            //    .on("dragend", function(d) {
            //      delete dragging[d];
            //      transition(d3.select(this)).attr("transform", "translate(" + x(d) + ")");
            //      transition(foreground).attr("d", path);
            //      background
            //          .attr("d", path)
            //        .transition()
            //          .delay(500)
            //          .duration(0)
            //          .attr("visibility", null);
            //    });
        
          // Add an axis and title.
          g.append("g")
              .attr("class", "axis")
              .each(function(d) { d3.select(this).call(axis.scale(y[d])); })
            .append("text")
              .style("text-anchor", "middle")
              .attr("y", -9)
              .text(function(d) { return d; });
        
          // Add and store a brush for each axis.
          // g.append("g")
          //     .attr("class", "brush")
          //     .each(function(d) {
          //       d3.select(this).call(y[d].brush = d3.svg.brush().y(y[d]).on("brushstart", brushstart).on("brush", brush));
          //     })
          //   .selectAll("rect")
          //     .attr("x", -8)
          //     .attr("width", 16);
        }

        function type(d) {
            return d;
        }

        d3.csv("data/a1-cereals.csv", type).then(renderVis)
        
        function position(d) {
          var v = dragging[d];
          return v == null ? x(d) : v;
        }
        
        function transition(g) {
          return g.transition().duration(500);
        }
        
        // Returns the path for a given data point.
        function path(d) {
          return d3.line()(dimensions.map( p => [position(p), y[p](d[p])] ))
        }
        
        function brushstart() {
          d3.event.sourceEvent.stopPropagation();
        }
        
        // Handles a brush event, toggling the display of foreground lines.
        function brush() {
          var actives = dimensions.filter(function(p) { return !y[p].brush.empty(); }),
              extents = actives.map(function(p) { return y[p].brush.extent(); });
          foreground.style("display", function(d) {
            return actives.every(function(p, i) {
              return extents[i][0] <= d[p] && d[p] <= extents[i][1];
            }) ? null : "none";
          });
        }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
svg {
  font: 10px sans-serif;
}

.background path {
  fill: none;
  stroke: #ddd;
  shape-rendering: crispEdges;
}

.foreground path {
  fill: none;
  stroke: steelblue;
}

.brush .extent {
  fill-opacity: .3;
  stroke: #fff;
  shape-rendering: crispEdges;
}

.axis line,
.axis path {
  fill: none;
  stroke: #000;
  shape-rendering: crispEdges;
}

.axis text {
  text-shadow: 0 1px 0 #fff, 1px 0 0 #fff, 0 -1px 0 #fff, -1px 0 0 #fff;
  cursor: move;
}
</style>
