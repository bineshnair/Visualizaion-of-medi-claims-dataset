# Visualizaion-of-medi-claims-dataset
The project has attempted to visualize some of the hidden patterns in the dataset. The dataset is a real-time big dataset taken from the Centers for Medicare and Medicaid (CMS), Maryland, US. The implementation has been done in d3.js post the data cleaning and preparation in R.
<!DOCTYPE html>
<meta charset='utf-8'>
<style>
body {
font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
position: relative;
width: 960px;
}
 
.axis text {
font: 10px sans-serif;
}
 
.axis path,
.axis line {
fill: none;
stroke: #000;
shape-rendering: crispEdges;
}
 
.bar {
fill: steelblue;
fill-opacity: .9;
}
 
.bar:hover {
fill: orange;
}
 
label {
position: absolute;
top: 10px;
right: 10px;
} 
</style>
<html>
<head>
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<link rel='stylesheet' href='style.css'>
</head>
</html>
<script type='text/javascript' src='script.js'></script>
<script>
d3.csv("data.csv",function (data) {
// CSV section
var body = d3.select('body')
var selectData = [ { "text" : "Log Mean Payment Amount" },
{ "text" : "Log Mean of Count of Line Items"},
{"text": "Log Mean Age"},
]
// Variables
var body = d3.select('body')
var margin = { top: 120, right: 150, bottom: 50, left: 50}
var h = 500 - margin.top - margin.bottom
var w = 1100 - margin.left - margin.right
var formatDecimal = d3.format('.2f') //Scale Precision in both the axes
// Scale
var colorScale = d3.scale.category20()
var xScale = d3.scale.linear()
.domain([
d3.min([0,d3.min(data,function (d) { return d['Log_Normalized_Mean_Age'] })]),
d3.max([0,d3.max(data,function (d) { return d['Log_Normalized_Mean_Age'] })])
])
.range([0,w])
var yScale = d3.scale.linear()
.domain([
d3.min([0,d3.min(data,function (d) { return d['Log_Normalized_Mean_Payment'] })]),
d3.max([0,d3.max(data,function (d) { return d['Log_Normalized_Mean_Payment'] })])
])
.range([h,0])
// SVG
var svg = body.append('svg')
.attr('height',h + margin.top + margin.bottom)
.attr('width',w + margin.left + margin.right)
.append('g')
.attr('transform','translate(' + margin.left + ',' + margin.top + ')')
// X-axis
var xAxis = d3.svg.axis()
.scale(xScale)
//.tickValues([0.00,0.75,1.50,2.25,3.00,3.75,4.25,5.00,5.75])
.innerTickSize([10])
.outerTickSize([10])
.tickFormat(formatDecimal)
.ticks(5)					//Number of datapoints required on the x-axis
.orient('bottom')
// Y-axis
var yAxis = d3.svg.axis()
.scale(yScale)
//.tickValues([0,75,150,225,300,375])
.innerTickSize([10])
.outerTickSize([10])
.tickFormat(formatDecimal)
//.ticks(4)				   //Number of datapoints required on the y-axis
.orient('left')
// Circles
var circles = svg.selectAll('circle')
.data(data)
.enter()
.append('circle')
.attr('cx',function (d) { return xScale(d['Log_Normalized_Mean_Age']) })
.attr('cy',function (d) { return yScale(d['Log_Normalized_Mean_Payment']) })
.attr('r',function (d) { return d['Log_Normalized_Mean_Payment']*10; })				//Radius of the circle's when the visualization is loaded
.attr('stroke','black')		//Colour of the arc of the circle's when the visualization is loaded
.attr('stroke-width',1)		//Width of the arc of the circle's when the visualization is loaded
.attr('fill',d3.rgb(86,180,233))
//.attr('fill',function (d,i) { return colorScale(i) })
.on('mouseover', function () {
d3.select(this)
.transition()
.duration(500)			//Time taken in milliseconds to undergo transition 
.attr('r',50)			//Radius of the circle when the mouse comes over it 
.attr('stroke-width',3)	//Width of the arc of the circle when the mouse comes over it
})
.on('mouseout', function () {
d3.select(this)
.transition()
.duration(500)			//Time taken in milliseconds to return to normal state after transition
.attr('r',function (d) { return d['Log_Normalized_Mean_Payment']*10; })			//Radius of the circle after the transition (Back to normal size)
.attr('stroke-width',1)	//Wisdth of the arc of the circle after the transition (Back to normal state)
})
.append('title') // Tooltip
.text(function (d) { return d.Place_of_Service +	//The title of each of the circles - representing each of the place of service for the claim
'\nMean Payment Amount: ' + formatDecimal(d['Mean_Payment']) +		//Payment amount will be displayed for each circle (place of service)
'\nTotal Line Items: ' + d['Sum_of_Line_Items'] +		//Number of Line Items will be displayed for each circle (place of service)
'\nAge: ' + d['Formatted_Value_of_Age'] })					//Age will be displayed for each circle (place of service)
.style("font-size","20px")
svg.append("text")
.attr("x",(w/2)+50)
.attr("y",0-(margin.top/2)-20)
.attr("text-anchor","middle")
.style("font-size","20px")
.style("font-style","bold")
.style("font-family","sans-serif")
.attr("fill","black")
.text("INTERCORRELATION OF CLAIM AMOUNT, PLACE OF SERVICE AND AGE");
// X-axis
svg.append('g')
.attr('class','axis')
.attr('id','xAxis')
.attr('transform', 'translate(0,' + h + ')')
.call(xAxis)
.append('text') // X-axis Label 	Setting the label on the x-axis
.attr('id','xAxisLabel')
.attr('y',-15)						//Y co-ordonite of the label
.attr('x',w)						//X co-ordinate of the label
.attr('dy','.71em')
.style('text-anchor','end')
.style("font-size","12px")
.text('Log of Mean Age')
// Y-axis
svg.append('g')
.attr('class','axis')
.attr('id','yAxis')
.call(yAxis)
.append('text') // y-axis Label 	Setting the label on the y-axis
.attr('id', 'yAxisLabel')	
.attr('transform','rotate(-90)')	
.attr('x',0)						//X co-ordinate of the label
.attr('y',5)						//Y co-ordinate of the label
.attr('dy','.71em')
.style('text-anchor','end')
.style("font-size","12px")
.text('Log of Mean Payment')
function yChange() {
var value = this.value // get the new y value
yScale // change the yScale
.domain([
d3.min([0,d3.min(data,function (d) { return d[value] })]),
d3.max([0,d3.max(data,function (d) { return d[value] })])
])
yAxis.scale(yScale) // change the yScale
d3.select('#yAxis') // redraw the yAxis
.transition().duration(1000)
.call(yAxis)
d3.select('#yAxisLabel') // change the yAxisLabel
.text(value)
d3.selectAll('circle') // move the circles
.transition().duration(1000)
.delay(function (d,i) { return i*100})
.attr('cy',function (d) { return yScale(d[value]) })
}
}) 
</script>
