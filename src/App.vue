<template>
  <div id="app">
    <Calc />
  </div>
</template>

<script>
import Calc from './components/Calc.vue'

export default {
  name: 'PlaybackTimeSavings',
  components: {
    Calc
  }
}
</script>

<style>
body {
  background: #272e38;
  color: #cacaca;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  /* color: #2c3e50; */
  margin-top: 60px;
}
</style>
