<template>
  <div>
    <h1>Film</h1>
    <svg id="vis3">
    </svg>
  </div>
</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'FilmVis',
  mounted: () => {
    let svgWidth = 1500;
    let svgHeight = 1000;

    let xProp = "year"
    let yProp = "length"
    let colorProp = "popularity"

    let vis3 = d3.select('#vis3')
        .attr('width', svgWidth)
        .attr('height', svgHeight);

    let dataGroup = vis3.append('g')
        .attr('class', 'films data-group');

    function renderFilms(data) {
      //console.log(data)
        let xScale = d3.scaleLinear()
            .domain(d3.extent(data, (d) => d[xProp]))
            .range([0, svgWidth])
        let yScale = d3.scaleLinear()
            .domain(d3.extent(data, (d) => d[yProp]))
            .range([svgHeight, 0])
        let colorScale = d3.scaleLinear()
            .domain(d3.extent(data, (d) => d[colorProp]))
            .range([0, 1])

        data = data.filter( (d) => d.subject == 'Action')

        let circles = dataGroup.selectAll('circle').data(data);

        circles.enter().append('circle')
            .attr('cx', (d) => xScale(d[xProp]))
            .attr('cy', (d) => yScale(d[yProp]))
            .attr('r', 5)
            .style('fill', (d) => 'rgba(0,0,0,' + colorScale(d[colorProp]) + ')')
            .on('mouseover', (d) => {
                document.getElementById('film-description').innerHTML = JSON.stringify(d)
            })

        circles.exit().remove();
    }
    
    function type(d) {
        /*
         * year         INT
         * length       INT
         * title        STRING
         * subject      STRING
         * actor        STRING
         * actress      STRING
         * director     STRING
         * popularity   INT
         * awards       STRING
         * image        STRING
         */
        d.year = +d.year;
        d.length = +d.length;
        d.popularity = +d.popularity;
        return d;
    }

    //console.log("a1-film")
    d3.csv('data/a1-film.csv', type).then(renderFilms)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
