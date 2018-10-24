<template>
    <div class="d3chart" align="center">
    </div>
</template>

<script>
    // https://bl.ocks.org/lorenzopub/0b09968e3d4970d845a5f45ed25595bb
    import * as d3 from 'd3'
    export default {
        name: "StackedAreaGraph",
        mounted() {
            let div = d3.select("body").append("div")
                .attr("class", "tooltip")
                .style("opacity", 0);

            let xFrame = 1000,
                yFrame = 500;

            let margin = {top: 50, right: 10, bottom: 50, left: 70},
                width = xFrame - margin.left - margin.right,
                height = yFrame - margin.top - margin.bottom;

            let parseDate = d3.timeParse('%Y');
            let formatNumber = d3.format(".0f"),
                formatMillion = function(x) { return formatNumber(x / 1e6); };

            let x = d3.scaleTime().range([0, width]);
            let y = d3
                .scalePow()
                .exponent(0.4)
                .range([height, 0]);

            let color = d3.scaleOrdinal(d3.schemeDark2);

            let xAxis = d3.axisBottom().scale(x).ticks(d3.timeYear);
            let yAxis = d3.axisLeft().scale(y).tickFormat(formatMillion); //.tickFormat(formatBillion);

            let area = d3.area()
                .x(function(d) {
                    return x(d.data.year); })
                .y0(function(d) { return y(d[0]); })
                .y1(function(d) { return y(d[1]); })
                .curve(d3.curveBasis);

            let stack = d3.stack();

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
                .text("Total Revenues by Source in Millions")
                .attr("class", "chart-sub-title");

            // Legend for red line
            svg.append("text")
                .attr("x", (width / 2))
                .attr("y", 12)
                .attr("text-anchor", "middle")
                .text("Actual Revenues")
                .attr("class", "chart-sub-legend");
            let legendData = [
                {"x": (width / 2) - 70, "y": 7},
                {"x": (width / 2) - 50, "y": 7}
            ];
            let legendLine = d3.line()
                .x(function(d) { return d.x })
                .y(function(d) { return d.y })
            svg.append("path")
                .data([legendData])
                .attr("class", "legend-area-actual")
                .attr("d", legendLine)
                .style("stroke", "red")
                .style("opacity", 1.0);


            // load the data from file
            d3.csv('data/stacked_area_exc_transferloans.csv')
                .then(function (data) {
                    // console.log(data);
                    color.domain(d3.keys(data[0]).filter(function(key) { return key !== 'year'; }));
                    let keys = data.columns.filter(function(key) { return key !== 'year'; });
                    data.forEach(function(d) {
                        d.year = parseDate(d.year);
                    });

                    let maxDateVal = d3.max(data, function(d){
                        var vals = d3.keys(d).map(function(key){ return key !== 'year' ? d[key] : 0 });
                        return d3.sum(vals);
                    });

                    // set domains
                    x.domain(d3.extent(data, function(d) { return d.year; }));
                    y.domain([0, maxDateVal]);

                    // set stacks
                    stack.keys(keys);
                    stack.order(d3.stackOrderAscending);
                    stack.offset(d3.stackOffsetNone);

                    // setup browser
                    let browser = svg.selectAll('.browser')
                        .data(stack(data))
                        .enter().append('g')
                        .attr('class', function(d){ return 'browser ' + d.key; })
                        .attr('fill-opacity', 0.5);



                    // append the path areas
                    browser.append('path')
                        .attr('class', 'area')
                        .attr('d', area)
                        .style('fill', function(d) { return color(d.key); })
                        .on("mouseover", function(d) {
                            let line = d3.line()
                                .x(function(d1) { return x(d1.year)})
                                .y(function(d1) { return y(d1[d.key])})
                                .curve(d3.curveBasis);

                            svg.append("path")
                                .data([data])
                                .attr("class", "area-actual")
                                .attr("d", line)
                                .style("stroke", "red");

                            svg.append("g").selectAll("text")
                                .data(data)
                                .enter()
                                .append("text")
                                .attr("class", "area-data-label")
                                .attr("x", function(d1) { return x(d1.year) })
                                .attr("y", function(d1) { return y(d1[d.key]) - 5})
                                .text(function (d1) {
                                    let format = d3.format(".2f"),
                                        formatMill = function(x) { return format(x / 1e6); };
                                    return  "$" + formatMill(d1[d.key])
                                });

                        })
                        .on("mousemove", function (d) {
                            d3.select(this)
                                .style("fill", color(d.key))
                                .attr('fill-opacity', 0.3);

                            let xPosition =  event.pageX;
                            let yPosition = event.pageY;

                            div.transition()
                                .style("opacity", .9);
                            div.html(d.key)
                                .style("left", (xPosition) + "px")
                                .style("top", (yPosition - 28) + "px");

                        })
                        .on("mouseout", function (d) {
                            d3.select(this)
                                .style("fill", color(d.key))
                                .attr('fill-opacity', "none");
                            div.transition()
                                .style("opacity", 0);
                            d3.select("path.area-actual").remove();
                            d3.selectAll("text.area-data-label").remove();
                        });

                    // let key,
                    //     yval = 0,
                    //     columnTotal = 0;
                    // for (key = 0; key < keys.length; key++) {
                    //     svg.selectAll("dot")
                    //         .data(data)
                    //         .enter()
                    //         .append("circle")
                    //         .attr("r", 3)
                    //         .attr("cx", function(d) { return x(d.year) })
                    //         .attr("cy", function(d) {
                    //             return y(d[keys[key]]);
                    //         });
                    //
                    // }


                    // add the x-axis
                    svg.append('g')
                        .attr('class', 'x axis')
                        .attr('transform', 'translate(0,' + height + ')')
                        .call(xAxis)

                    // text label for x-axis
                    svg.append("text")
                        .attr("transform",
                            "translate(" + (width/2) + " ," +
                            (height + margin.top) + ")")
                        .style("text-anchor", "middle")
                        .text("Year");

                    // add the y-axis
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
                });
        },
    }
</script>

<style >
    div.tooltip {
        position: absolute;
        text-align: center;
        width: auto;
        min-width: 60px;
        height: auto;
        min-height: 10px;
        padding: 2px;
        margin: 2px;
        font: 12px sans-serif;
        background: lightsteelblue;
        border: 0px;
        border-radius:2px;
        pointer-events: none;
        color: black;
    }
    .area-actual {
        fill: none;
    }

    .area-data-label {
        font-size: 0.75em;
        text-align: center;
    }

    text.chart-title {
        font-size: 24px;
    }

    text.chart-sub-legend {
        font-size: 12px;
    }

</style>
