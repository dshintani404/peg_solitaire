<template>
  <div id="app">
    <pegsolitaire/>
  </div>
</template>

<script>
import pegsolitaire from './components/pegsolitaire.vue'

export default {
  name: 'app',
  components: {
    pegsolitaire
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
  margin-top: 60px;
}
</style>
