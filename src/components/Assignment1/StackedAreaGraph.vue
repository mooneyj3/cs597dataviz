<template>
    <div class="d3chart" align="center">
    </div>
</template>

<script>
    // https://bl.ocks.org/lorenzopub/0b09968e3d4970d845a5f45ed25595bb
    /*
        TODO: mouseover values -- ideally year and amount
        TODO: on mousemove line, display the line name
        TODO: (@time) add/remove axis hover lineslines
        TODO: (@time) redo colors to bolds
        TODO: (@time) background image
     */
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
                .exponent(0.5)
                .range([height, 0]);

            let color = d3.scaleOrdinal(d3.schemeDark2);

            let xAxis = d3.axisBottom().scale(x).ticks(d3.timeYear);;
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
                        .on("mousemove", function (d) {
                            d3.select(this)
                                .style("fill", color(d.key))
                                .attr('fill-opacity', 0.3);

                            let xPosition =  event.pageX;
                            let yPosition = event.pageY;

                            let d3Mouse = d3.mouse(this);

                            let yearOffset = width / 17;

                            let xStartYear = Math.floor(d3Mouse[0] / yearOffset);
                            let xEndYear = Math.ceil(d3Mouse[0] / yearOffset);
                            // console.log("s: " + d[xStartYear][0] + "  e: " + d[xStartYear][1]);
                            // x -> [0, 880]
                            // y -> [400, 880]

                            div.transition()
                                .duration(200)
                                .style("opacity", .9);
                            div.html(d.key + "<br />" + d.year)
                                .style("left", (xPosition) + "px")
                                .style("top", (yPosition - 28) + "px");

                        })
                        .on("mouseout", function (d) {
                            d3.select(this)
                                .style("fill", color(d.key))
                                .attr('fill-opacity', "none");
                            div.transition()
                                .duration(500)
                                .style("opacity", 0);
                        });

                    let key,
                        yval = 0,
                        columnTotal = 0;
                    for (key = 0; key < keys.length; key++) {
                        svg.selectAll("dot")
                            .data(data)
                            .enter()
                            .append("circle")
                            .attr("r", 3)
                            .attr("cx", function(d) { return x(d.year) })
                            .attr("cy", function(d) {
                                return y(d[keys[key]]);
                            });

                    }


                    // add the x-axis
                    svg.append('g')
                        .attr('class', 'x axis')
                        .attr('transform', 'translate(0,' + height + ')')
                        .call(xAxis);

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
        methods: {
        }

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
        font: 12px sans-serif;
        background: lightsteelblue;
        border: 0px;
        border-radius: 8px;
        pointer-events: none;
    }
</style>
