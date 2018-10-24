<template>
    <div class="d3chart"></div>
</template>

<script>
    // https://bl.ocks.org/pstuffa/26363646c478b2028d36e7274cedefa6
    // Best Reference material:  https://bl.ocks.org/d3noob/4db972df5d7efc7d611255d1cc6f3c4f
    /*
        TODO: dots on mouseover values
     */
    import * as d3 from 'd3'
    export default {
        name: "LineChart",
        mounted() {
            let div = d3.select("body").append("div")
                .attr("class", "tooltip")
                .style("opacity", 0);

            let xFrame = 1000,
                yFrame = 500;

            let margin = {top: 50, right: 10, bottom: 70, left: 70},
                width = xFrame - margin.left - margin.right,
                height = yFrame - margin.top - margin.bottom;

            let parseDate = d3.timeParse('%Y');
            let formatNumber = d3.format(".0f"),
                formatMillion = function(x) { return formatNumber(x / 1e6); };

            let x = d3.scaleTime().range([0, width]);
            let y = d3.scalePow().exponent(0.5)
                .range([height, 0]);

            let xAxis = d3.axisBottom()
                .scale(x)
                .ticks(d3.timeYear);

            let yAxis = d3.axisLeft().scale(y).tickFormat(formatMillion); //.tickFormat(formatBillion);

            let svg = d3.select(this.$el)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')');

            // Chart Title
            svg.append("text")
                .attr("x", (width / 2))
                .attr("y", 0 - (margin.top /2 ))
                .attr("text-anchor", "middle")
                .text("California State Revenues (2000 - 2017)")
                .attr("class", "chart-title");

            // Chart Sub - Title
            svg.append("text")
                .attr("x", (width / 2))
                .attr("y", 0 - (0.25 * (margin.top / 2 )))
                .attr("text-anchor", "middle")
                .text("Total Revenues by Fund & Revenue Type in Millions")
                .attr("class", "chart-sub-title");



            d3.csv('data/line_graph.csv')
                .then(function (data) {
                    // let keys = data.columns.filter(function(key) { return key !== 'year'; });
                    data.forEach(function(d) {
                        d.year = parseDate(d.year);
                    });

                    let colorMap = [
                        '#000080', '#6495ED', // funds
                        '#f47461','#db4551','#b81b34','#8b0000' // other categories
                    ];

                    // let maxYVal = d3.max(data, function(d){
                    //     return d3.max(d3.keys(d).map(function(key){ return d[key] }));
                    // });

                    x.domain(d3.extent(data, function(d) { return d.year; }));
                    // y.domain([-20000000, 155833473]);
                    y.domain([-6303028, 155833473]);

                    // setup lines
                    // Year,General Fund,Special Funds,Major Revenue,Minor Revenue,SCO Adjustments,Transfers & Loans
                    // console.log(data);

                    // console.log(data.columns[1]);

                    let i;
                    let lines = [];


                    for (i = 1 ; i < data.columns.length; i++) {
                        let column = data.columns[i];
                        let myIndex = i;

                        lines.push(
                            d3.line()
                                .x(function(d) { return x( d.year ) })
                                .y(function(d) { return y( d[column] );})
                        );

                        svg.append("path")
                            .data([data])
                            .attr("class", "line")
                            .attr("d", lines[i - 1])
                            .style("stroke", colorMap[i-1]);

                        svg.selectAll("dot")
                            .data(data)
                            .enter()
                            .append("circle")
                            .attr("r", 3)
                            .attr("cx", function(d) {return x(d.year)})
                            .attr("cy", function (d)  { return y( d[column]);})
                            .style("fill", colorMap[i-1])
                            .on("mouseover", function(d) {

                                // let yearLookup = d.year.getFullYear() - 2000;
                                let myCat = data.columns[myIndex];
                                // console.log(d[myCat]);

                                let format = d3.format(".2f"),
                                    formatMill = function(x) { return format(x / 1e6)};

                                d3.select(this)
                                    .attr('r', 5);

                                let xPosition =  event.pageX;
                                let yPosition = event.pageY;

                                div.transition()
                                    .style("opacity", .9);
                                div.html("<b>" + data.columns[myIndex] + "</b>" + "<br />"
                                    + "$" + formatMill(d[myCat]))
                                    .style("left", (xPosition) + "px")
                                    .style("top", (yPosition - 28) + "px");

                            })
                            .on("mouseout", function() {
                                d3.select(this)
                                    .attr('r', 3)
                                div.transition()
                                    .style("opacity", 0);
                            });

                        svg.append("text")
                            .attr("x", (margin.left) * (i * 2 ) - 80)
                            .attr("y", 12 )
                            .attr("class", "line-labels")
                            .text(data.columns[i])
                            .style("stroke", colorMap[i-1])
                    }

                    // Add the x-axis
                    svg.append('g')
                        .attr('class', 'x axis')
                        .attr('transform', 'translate(0,' + height + ')')
                        .call(xAxis);

                    // text label for x-axis
                    svg.append("text")
                        .attr("transform",
                            "translate(" + (width/2) + " ," +
                            (height + margin.top - 15 ) + ")")
                        .style("text-anchor", "middle")
                        .text("Year");

                    svg.append('g')
                        .attr('class', 'y axis')
                        .call(yAxis);

                    // text label for y-axis
                    svg.append("text")
                        .attr("transform", "rotate(-90)")
                        .attr("y", 0 - margin.left)
                        .attr("x",0 - (height / 2))
                        .attr("dy", "1em")
                        .style("text-anchor", "middle")
                        .text("Revenue (millions)");

                }) // End of csv.then
        }
    }
</script>

<style>
    .line {
        fill: none;
        stroke: steelblue;
        stroke-width: 2px;
    }
</style>
