// This component is the first attempt at incorporating d3 into Vue
// It pulls in a vanilla JS approach and doesn't really leverage Vue
// Next TestGraph should make a better attempt to leverage Vue

<template>
  <section>
    <h1>Test Graph</h1>
    <div class="graph"></div>
  </section>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'testGraph',
  data () {
    return {
      msg: 'Welcome to Your Vue.js PWA'
    }
  },
  mounted () {
    this.startGraph()
  },
  methods: {
    startGraph () {
      console.log('graph started')
      // https://bl.ocks.org/pstuffa/26363646c478b2028d36e7274cedefa6
      // Use margin convention practice
      var margin = {
        top: 50,
        right: 50,
        left: 50,
        bottom: 50
      }
      var width = 800 - margin.left - margin.right
      var height = 600 - margin.top - margin.bottom

      // number of data points (static)
      var n = 30

      // X scale will use the index of our data
      var xScale = d3.scaleLinear()
        .domain([0, n - 1]) // input
        .range([0, width]) // output

      // Y scale will use the randomly generated number
      var yScale = d3.scaleLinear()
        .domain([0, 1]) // input
        .range([height, 0]) // output

      // d3 line generator
      var line = d3.line()
        .x(function (d, i) { return xScale(i) }) // set the x values for the line generator
        .y(function (d) { return yScale(d.y) }) // set the y values for the line generator
        .curve(d3.curveMonotoneX) // apply smoothing

      // An array of objects of length N. Each object has key -> value pair, the key being "y" and the value is a random number
      var dataset = d3.range(n).map(function (d) { return { 'y': d3.randomUniform(1)() } })

      // Add the SVG to the page and apply the margin
      var svg = d3.select('.graph').append('svg')
        .attr('width', width + margin.left + margin.right)
        .attr('height', height + margin.top + margin.bottom)
        .append('g')
        .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')')

      // call the x axis in a group tag
      svg.append('g')
        .attr('class', 'x axis')
        .attr('transform', 'translate(0,' + height + ')')
        .call(d3.axisBottom(xScale)) // create an axis component with d3.axisBottom

      // class the y axix
      svg.append('g')
        .attr('class', 'y axis')
        .call(d3.axisLeft(yScale)) // create an axis component with d3.axisLeft

      // append the path, bind the data, and call the line generator
      svg.append('path')
        .datum(dataset) // bind the data to the line
        .attr('class', 'line')
        .attr('d', line) // call the line generator

      // append a circle for each datapoint
      svg.selectAll('.dot')
        .data(dataset)
        .enter().append('circle') // use the enter().append() method
        .attr('class', 'dot')
        .attr('cx', function (d, i) { return xScale(i) })
        .attr('cy', function (d) { return yScale(d.y) })
        .attr('r', 5)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.graph {
  outline: 1px solid #ccc;
}

.line {
  fill: none;
  stroke: steelblue;
  stroke-width: 3;
}

.dot {
  fill: red;
  stroke: #fff;
}
</style>
