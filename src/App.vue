<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <StreamGraph></StreamGraph>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import StreamGraph from "./components/Assignment1/StreamGraph";

export default {
  name: 'app',
  components: {
      StreamGraph,
    HelloWorld
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
