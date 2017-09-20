<template>
  <div id="app">
    <img src="./assets/logo.png">
    <hello></hello>
    <ToolTip/>
  </div>
</template>

<script>
import Hello from './components/Hello'
import ToolTip from './components/ToolTip'

export default {
  name: 'app',
  components: {
    Hello,
    ToolTip
  }
}
</script>

<style lang="sass">
#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 60px

</style>
