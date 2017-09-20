<template>
  <div class="tool-tip">
    <p>{{data.country}}</p>
  </div>
</template>

<script>
export default{
  name:'ToolTip',
  props: ['data'],
}
</script>

<style lang="sass">
  .tool-tip
    display: none
    font-family: sans-serif
    position: absolute
    background-color: #88f
    box-shadow: 0 0 4px rgba(0,0,0,.14), 0 4px 8px rgba(0,0,0,.28)

  .tool-tip p
    color: white
    margin: 5px 10px 5px 10px


</style>
