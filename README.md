# d3-gdp-bar-chart
HTML CODE
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>GDP Bar Chart</title>
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div id="chart-container">
    <h1 id="title">United States GDP</h1>
    <div id="tooltip" style="opacity:0; position:absolute;"></div>
    <svg id="chart"></svg>
  </div>
  <script src="script.js"></script>
  <script src="https://cdn.freecodecamp.org/testable-projects-fcc/v1/bundle.js"></script>
</body>
</html>

CSS (style.css)
body {
  font-family: Arial, sans-serif;
  display: flex;
  justify-content: center;
  padding-top: 50px;
  background-color: #f9f9f9;
}

#chart-container {
  position: relative;
}

#chart {
  background-color: #fff;
  border: 1px solid #ccc;
}

.bar {
  fill: steelblue;
}

.bar:hover {
  fill: orange;
}

#tooltip {
  background-color: rgba(0,0,0,0.7);
  color: #fff;
  padding: 5px 10px;
  border-radius: 3px;
  pointer-events: none;
  font-size: 12px;
}
JavaScript (script.js)
const url = "https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/GDP-data.json";

fetch(url)
  .then(response => response.json())
  .then(data => {
    const dataset = data.data;

    const margin = { top: 50, right: 20, bottom: 50, left: 80 };
    const width = 800 - margin.left - margin.right;
    const height = 500 - margin.top - margin.bottom;

    const svg = d3.select("#chart")
                  .attr("width", width + margin.left + margin.right)
                  .attr("height", height + margin.top + margin.bottom)
                  .append("g")
                  .attr("transform", `translate(${margin.left},${margin.top})`);

    // X scale
    const xScale = d3.scaleTime()
                     .domain([new Date(dataset[0][0]), new Date(dataset[dataset.length-1][0])])
                     .range([0, width]);

    // Y scale
    const yScale = d3.scaleLinear()
                     .domain([0, d3.max(dataset, d => d[1])])
                     .range([height, 0]);

    // X axis
    const xAxis = d3.axisBottom(xScale);
    svg.append("g")
       .attr("id", "x-axis")
       .attr("transform", `translate(0, ${height})`)
       .call(xAxis);

    // Y axis
    const yAxis = d3.axisLeft(yScale);
    svg.append("g")
       .attr("id", "y-axis")
       .call(yAxis);

    // Tooltip
    const tooltip = d3.select("#tooltip");

    // Bars
    svg.selectAll(".bar")
       .data(dataset)
       .enter()
       .append("rect")
       .attr("class", "bar")
       .attr("x", d => xScale(new Date(d[0])))
       .attr("y", d => yScale(d[1]))
       .attr("width", width / dataset.length)
       .attr("height", d => height - yScale(d[1]))
       .attr("data-date", d => d[0])
       .attr("data-gdp", d => d[1])
       .on("mouseover", (event, d) => {
         tooltip.transition().duration(200).style("opacity", 0.9);
         tooltip.html(`${d[0]}<br>$${d[1]} Billion`)
                .attr("data-date", d[0])
                .style("left", (event.pageX + 10) + "px")
                .style("top", (event.pageY - 28) + "px");
       })
       .on("mouseout", () => {
         tooltip.transition().duration(200).style("opacity", 0);
       });
  });
