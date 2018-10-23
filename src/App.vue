<template>
  <div id="app">
      <!--<StackedAreaGraph></StackedAreaGraph>-->
      <LineChart></LineChart>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import StackedAreaGraph from "./components/Assignment1/StackedAreaGraph";
import LineChart from "./components/Assignment1/LineChart";

export default {
  name: 'app',
  components: {
      LineChart,
      StackedAreaGraph,
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
