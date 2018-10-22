<template>

</template>

<script>
    // https://bl.ocks.org/lorenzopub/0b09968e3d4970d845a5f45ed25595bb
    import * as d3 from 'd3'
    export default {
        name: "StreamGraph",
        mounted() {
            let div = d3.select("body").append("div")
                .attr("class", "tooltip")
                .style("opacity", 0);

            let xFrame = 1200,
                yFrame = 750;

            let margin = {top: 20, right: 60, bottom: 30, left: 70},
                width = xFrame - margin.left - margin.right,
                height = yFrame - margin.top - margin.bottom;

            let parseDate = d3.timeParse('%Y');

            let x = d3.scaleTime().range([0, width]);
            let y = d3.scaleLinear().range([height, 0]);

            let color = d3.scaleOrdinal(d3.schemeCategory10);

            let xAxis = d3.axisBottom().scale(x);
            let yAxis = d3.axisLeft().scale(y); //.tickFormat(formatBillion);

            let area = d3.area()
                .x(function(d) {
                    return x(d.data.year); })
                .y0(function(d) { return y(d[0]); })
                .y1(function(d) { return y(d[1]); });

            let stack = d3.stack();

            let svg = d3.select(this.$el)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')');

            // d3.csv('data/TestData.csv', function(d) {
            //     return {
            //         // abandonedProperty: +d["Abandoned Property"],
            //         // adjustmentsToReconcileToController: +d["Adjustments to Reconcile to Controller"],
            //         // alcoholicBeveragesTaxesAndFees: +d["Alcoholic Beverages Taxes & Fees"],
            //         // allOtherMinorRevenue: +d["All Other Minor Revenue"],
            //         // californiaStateUniversityFees: +d["California State University Fees"],
            //         // cigaretteTax: +d["Cigarette Tax"],
            //         // corporationTax: +d["Corporation Tax"],
            //         // emergencyTelephoneUsersSurcharge: +d["Emergency Telephone Users Surcharge"],
            //         // estateInheritanceAndGiftTax: +d["Estate, Inheritance & Gift Tax"],
            //         // horseRacingPariMutualLicenseFees: +d["Horse Racing (Pari-mutual License Fees)"],
            //         // incomeFromPooledMoneyInvestments: +d["Income from Pooled Money Investments"],
            //         // incomeFromSurplusMoneyInvestments: +d["Income from Surplus Money Investments"],
            //         // insuranceGrossPremiumTax: +d["Insurance Gross Premium Tax"],
            //         // motorVehicleFuelTaxDiesel: +d["Motor Vehicle Fuel Tax (Diesel)"],
            //         // motorVehicleFuelTaxGasoline: +d["Motor Vehicle Fuel Tax (Gasoline)"],
            //         // motorVehicleLicenseInLieuFees: +d["Motor Vehicle License (In-Lieu) Fees"],
            //         // motorVehicleRegistration: +d["Motor Vehicle Registration"],
            //         // personalIncomeTax: +d["Personal Income Tax"],
            //         // retailSalesAndUseTax: +d["Retail Sales and Use Tax"],
            //         // retailSalesAndUseTaxFiscalRecovery: +d["Retail Sales and Use Tax (Fiscal Recovery)"],
            //         // retailSalesAndUseTaxRealignment: +d["Retail Sales and Use Tax (Realignment)"],
            //         // settlementsAndJudgments: +d["Settlements & Judgments"],
            //         // stateLandsRoyalties: +d["State Lands Royalties"],
            //         // telecommunicationsTax: +d["Telecommunications Tax"],
            //         // trailerCoachLicenseInLieuFees: +d["Trailer Coach License (In-Lieu) Fees"],
            //         // transfersAndLoans: +d["Transfers & Loans"],
            //         // trialCourtRevenues: +d["Trial Court Revenues"]
            //     };})
            d3.csv('data/streamgraphdata.csv')
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

                    let colorRange = ["#045A8D", "#2B8CBE", "#74A9CF", "#A6BDDB", "#D0D1E6", "#F1EEF6"];

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

                            // console.log("x: " + xPosition + ", y: " + yPosition);

                            div.transition()
                                .duration(200)
                                .style("opacity", .9);
                            div.html(d.key )
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

                    svg.append('g')
                        .attr('class', 'x axis')
                        .attr('transform', 'translate(0,' + height + ')')
                        .call(xAxis);

                    svg.append('g')
                        .attr('class', 'y axis')
                        .call(yAxis);

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
