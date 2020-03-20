<template>
  <div>
    <h1>Mutual Funds</h1>

    <svg id="vis5">
    </svg>

    <div id="focusbox">
    </div>

    <h2> Description </h2>
    <p>
    </p>
  </div>
</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'MutualFunds',
  mounted: function() {
    let svgWidth = 1500;
    let svgHeight = 1000;

    let vis3 = d3.select('#vis5')
        .attr('width', svgWidth)
        .attr('height', svgHeight);

    let dataGroup = vis3.append('g')
        .attr('class', 'mutual data-group');

    let _this = this;
    function renderFilms(data) {

    }
    
    function type(d) {
        
        return d;
    }

    //console.log("a1-film")
    d3.csv('data/a1-mutualfunds.csv', type).then(renderFilms)
  },
  data: () => {
    return {
    };
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#focusbox {
  background-color: grey;
}

</style>
