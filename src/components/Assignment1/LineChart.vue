<template>
    <div></div>
</template>

<script>
    // https://bl.ocks.org/pstuffa/26363646c478b2028d36e7274cedefa6
    // Best Reference material:  https://bl.ocks.org/d3noob/4db972df5d7efc7d611255d1cc6f3c4f
    import * as d3 from 'd3'
    export default {
        name: "LineChart",
        mounted() {

            let xFrame = 1000,
                yFrame = 500;

            let margin = {top: 50, right: 50, bottom: 50, left: 100},
                width = xFrame - margin.left - margin.right,
                height = yFrame - margin.top - margin.bottom;

            let parseDate = d3.timeParse('%Y');

            let x = d3.scaleTime().range([0, width]);
            let y = d3.scalePow().range([height, 0]);


            let xAxis = d3.axisBottom().scale(x);
            let yAxis = d3.axisLeft().scale(y); //.tickFormat(formatBillion);

            let svg = d3.select(this.$el)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')');

            d3.csv('data/line_graph.csv')
                .then(function (data) {
                    let keys = data.columns.filter(function(key) { return key !== 'year'; });
                    data.forEach(function(d) {
                        d.year = parseDate(d.year);
                    });

                    let maxYVal = d3.max(data, function(d){
                        return d3.max(d3.keys(d).map(function(key){ return d[key] }));
                    });

                    x.domain(d3.extent(data, function(d) { return d.year; }));
                    // y.domain([-20000000, 155833473]);
                    y.domain([-6303028, 155833473]);

                    // setup lines
                    // Year,General Fund,Special Funds,Major Revenue,Minor Revenue,SCO Adjustments,Transfers & Loans
                    // console.log(data);
                    let generalFundLine = d3.line()
                        .x(function(d) { return x( d.year ) })
                        .y(function(d) { return y( d['General Fund'] );});

                    let specialFundsLine = d3.line()
                        .x(function (d)  { return x( d.year) })
                        .y(function (d)  { return y( d['Special Funds']);});

                    let majorRevenueLine = d3.line()
                        .x(function (d)  { return x( d.year) })
                        .y(function (d)  { return y( d['Major Revenue']);});

                    let minorRevenueLine = d3.line()
                        .x(function (d)  { return x( d.year) })
                        .y(function (d)  { return y( d['Minor Revenue']);});

                    let scoAdjustmentsLine = d3.line()
                        .x(function (d)  { return x( d.year) })
                        .y(function (d)  { return y( d['SCO Adjustments']);});

                    let transferLoanLine = d3.line()
                        .x(function (d)  { return x( d.year) })
                        .y(function (d)  { return y( d['Transfers & Loans']);});

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", generalFundLine);

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", specialFundsLine)
                        .style("stroke", "red");

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", majorRevenueLine)
                        .style("stroke", "blue");

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", minorRevenueLine)
                        .style("stroke", "green");

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", scoAdjustmentsLine)
                        .style("stroke", "violet");

                    svg.append("path")
                        .data([data])
                        .attr("class", "line")
                        .attr("d", transferLoanLine)
                        .style("stroke", "pink");


                    svg.append('g')
                        .attr('class', 'x axis')
                        .attr('transform', 'translate(0,' + height + ')')
                        .call(xAxis);

                    svg.append('g')
                        .attr('class', 'y axis')
                        .call(yAxis);

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
