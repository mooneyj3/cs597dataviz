<template>
    <div id="app">
        <div id="wrapper">
            <h1>Assignment 1</h1>
            <h2>Jonny Mooneyham
                <br/>
                CS 597 - Data Visualization
            </h2>
            <div id="chart1">
                <h3>Comparing Different Sources of Revenue</h3>
                <p>
                    Here I used a stacked area chart so that one can see how the different sources of revenue contribute
                    to the overall state revenues.  Exponential expansion was used on the y-axis so small sources of
                    revenue are visible.  Interaction is enabled to show the actual values along a line graph when hovering
                    over a specific region.
                </p>
                <p>
                    This graph was generated with the help of this contributor on <a href="bl.ocks.org">bl.ocks.org</a>.
                    <br /><a href="https://bl.ocks.org/lorenzopub/0b09968e3d4970d845a5f45ed25595bb">https://bl.ocks.org/lorenzopub/0b09968e3d4970d845a5f45ed25595bb</a>
                </p>
                <StackedAreaGraph></StackedAreaGraph>
            </div>
            <div id="chart2">
                <h3>Comparing Funds and Types of Revenues</h3>
                <p>
                    In this graph, I used a multi-line chart to show the different contributions to revenue.  Again the
                    y-axis is exponentially adjusted to allow comparisons of different revenues that would otherwise be
                    compressed by scaling.
                </p>
                <p>
                    This graph was generated with the help of this contributor on <a href="bl.ocks.org">bl.ocks.org</a>.
                    <br /><a href="https://bl.ocks.org/pstuffa/26363646c478b2028d36e7274cedefa6">https://bl.ocks.org/pstuffa/26363646c478b2028d36e7274cedefa6</a>
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
