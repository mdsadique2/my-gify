<template>
  <div id="app">
    <link rel="stylesheet"
       href="https://use.fontawesome.com/releases/v5.2.0/css/all.css"
       integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ"
       crossorigin="anonymous">
    <!-- default landing page to search Gifs -->
    <SearchPage></SearchPage>
  </div>
</template>

<script>
import './style/style.css'
import Vue from 'vue'
import SearchPage from './pages/SearchPage.vue'

export default {
  name: 'app',
  components: {
    SearchPage
  },
  mounted () {

  },
  created () {
  },
  data () {
    return {
      info: '--'
    }
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
  position: relative;
}

</style>
