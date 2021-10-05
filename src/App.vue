<template>
  <div id="app">
    <Navbar />
    <Artigos msg="Meu artigos"/>
  </div>
</template>

<script>
import Artigos from './components/Artigos.vue'
import Navbar from './components/Navbar.vue'

export default {
  name: 'App',
  components: {
    Artigos,
    Navbar
  }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
  }
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
