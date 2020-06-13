<template>
  <div id="app">
    <MainView msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import MainView from './components/MainView.vue'
import 'bootstrap/dist/css/bootstrap.min.css'
import './assets/font-awesome/css/font-awesome.min.css'
import 'jquery/src/jquery.js'
import 'bootstrap/dist/js/bootstrap.min.js'
import 'popper.js/dist/popper.min.js'

export default {
  name: 'App',
  components: {
    MainView
  }
}
</script>

<style lang="scss">
// #app {
//   font-family: Avenir, Helvetica, Arial, sans-serif;
//   -webkit-font-smoothing: antialiased;
//   -moz-osx-font-smoothing: grayscale;
//   text-align: center;
//   color: #2c3e50;
//   margin-top: 60px;
// }
</style>
