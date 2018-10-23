<template>
    <div id="app">
        <div id="wrapper">
            <h1>Assignment 1</h1>
            <h2>Jonny Mooneyham
                <br/>
                CS 597 - Data Visualization
            </h2>
            <div id="chart1">
                <h3>Graph 1: Look things</h3>
                <p>
                    Exploring the relationship...
                </p>
                <StackedAreaGraph></StackedAreaGraph>
            </div>
            <div id="chart2">
                <h3>Chart 2: Look some lines</h3>
                <p>
                    some other stuff I need to talk about for this graph
                </p>
                <LineChart></LineChart>
            </div>
        </div>
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
        /*text-align: center;*/
        color: #2c3e50;
        margin-top: 60px;
    }

    #app h1, h2 {
        text-align: center;
    }

    #app h3 {
        padding: 10px;
        margin-bottom: 0;
        padding-bottom: 0;
    }

    #app p {
        padding-top: 5px;
        padding-bottom: 5px;
        padding-left: 10px;
        padding-right: 10px;
    }

    #wrapper {
        margin: 0 auto;
        width: 90%;
        background: #99ccff;
        border: 1px solid #878e63;
        box-shadow: 0 0 10px rgba(12, 3, 25, 0.8);
    }

    body {
        background-color: #CACACA;
        margin: 0px;
        font-family: Verdana;
        color: #697e83;
        font-size: 14px;
    }

    .d3chart {
        background-color: aliceblue;
        margin: 10px;
        box-shadow: 0 0 10px rgba(12, 3, 25, 0.8);
        width: auto;
        display: inline-block;

    }


</style>
