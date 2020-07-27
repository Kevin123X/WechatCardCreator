<template>
  <div id="app">
    <Creator/>
  </div>
</template>

<script>
import Creator from './components/Creator'

export default {
  name: 'app',
  components: {
    Creator
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
