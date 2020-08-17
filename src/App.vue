<template>
  <div id="app">
    <GameTheSimon />
  </div>
</template>

<script>
import GameTheSimon from './components/GameTheSimon'

export default {
  name: 'App',
  components: {
    GameTheSimon
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
}
</style>
