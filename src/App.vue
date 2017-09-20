<template>
  <div id="app">
    <div class="github">
      <a href="https://github.com/xevrem/forcegraph"><i class="fa fa-github" aria-hidden="true"></i> </a>
    </div>
    <div class="chart-area">
      <div class='header'>
        <h1>State Contiguity Force Graph</h1>
      </div>
      <svg class="graph" width="600" height="600"></svg>
      <div class="flag-hack"></div>
    </div>
    <ToolTip :data="tt_data"/>
  </div>
</template>

<script>
import ToolTip from './components/ToolTip';
import axios from 'axios';
import * as d3 from 'd3'

export default {
  name: 'app',
  data: function(){
    return{
      data:[],
      tt_data:{},
      simulation: null,
    }
  },
  components: {
    ToolTip
  },
  mounted: async function(){
    var resp = await axios.get('https://raw.githubusercontent.com/DealPete/forceDirected/master/countries.json');
    this.data = resp.data;
    this.do_d3();
  },
  methods:{
    do_d3:function(){
      //console.log(this.data);
      let svg = d3.select('svg'),
        width = +svg.attr('width'),
        height = +svg.attr('height');

      let swidth = window.innerWidth;
      let sheight = window.innerHeight-300;
      let mody = sheight/2 - height/2+100;
      let modx = swidth/2 - width/2;

      svg
        .style('position','absolute')
        //.style('top', mody+'px')
        //.style('left', modx+'px');

      let div = d3.selectAll('.chart-area')
        .style('top', mody+'px')
        .style('left', modx+'px')
        .style('width', width+'px')
        .style('height', (height+100)+'px')
        .style('background-color', 'orange');

      let tooltip = d3.select('.tool-tip');

      let simulation = d3.forceSimulation()
        .force('link', d3.forceLink().id(d=>{return d.index;}).distance(30).strength(1))
        .force('charge', d3.forceManyBody().strength(-5).distanceMax(100).distanceMin(16))
        .force('center', d3.forceCenter(width/2, height/2))
        .force('collide', d3.forceCollide().radius(16));

      let link = svg.append('g')
          .attr('class', 'links')
        .selectAll('line')
        .data(this.data.links)
        .enter().append('line')
          .attr('stroke-width', '1')
          .attr('stroke', '#333');

      let node = d3.select('.flag-hack')
        .selectAll('img')
        .data(this.data.nodes)
        .enter().append('img')
          .attr('class', d =>{ return 'flag flag-'+d.code;})
          .on('mouseover', d =>{
            tooltip
              .style('display', 'inline-block')
              .style('left', d3.event.pageX + 'px')
              .style('top', d3.event.pageY - 50 + 'px');
            //console.log(d);
            this.tt_data = d;
          })
          .on('mouseout', () =>{
            tooltip
              .style('display', 'none');
            this.tt_data = {};
          })
          .call(d3.drag()
          .on("start", this.drag_started)
          .on("drag", this.dragged)
          .on("end", this.drag_ended));

      var foo = false;

      simulation
        .nodes(this.data.nodes)
        .on('tick',function(){
          link
            .attr("x1", d => { return d.source.x; })
            .attr("y1", d => { return d.source.y; })
            .attr("x2", d => { return d.target.x; })
            .attr("y2", d => { return d.target.y; });
          node
            .style('left', d => { return (d.x-8) +'px'; })
            .style('top', d => { return (d.y+95)+'px'; });
        });

      simulation.force('link')
        .links(this.data.links);

      this.simulation = simulation;
    },
    drag_started:function(d){
      if (!d3.event.active) this.simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    },
    dragged: function(d){
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    },
    drag_ended: function(d){
      if (!d3.event.active) this.simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    }
  }
}
</script>

<style lang="sass">

body
  background: linear-gradient(180deg, hsl(240,25,90), hsl(320,25,90)) no-repeat center center fixed
  font-family: sans-serif

a
  font-size: 40px
  color: hsl(271,68,32)

.chart-area
  position: absolute
  box-shadow: 0 0 4px rgba(0,0,0,.14), 0 4px 8px rgba(0,0,0,.28)

.flag-hack
  position: absolute
  top: 0px

.flag
  position: absolute

.graph
  background-color: #fff

.header
  margin: 0px
  text-align: center
  background-color: #fff
  height: 100px

.header h1
  background-color: #88f
  color: #fff
  margin: 0px
  padding: 15px 0 15px 0
  box-shadow: 0 4px 8px rgba(0,0,0,.28)

</style>
