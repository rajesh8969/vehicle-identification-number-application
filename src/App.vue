<template>
  <div id="app">
    <VinComponent msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
  
import VinComponent from './components/VinComponent.vue'

export default {
  name: 'app',
  components: {
    VinComponent
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
}
body {
  margin: 0;
  background-color: #999 !important;
}
</style>
