<script>
  import * as d3 from 'd3';

  export let data;
  export let fullData;
  export let variable; // Should be "City" in this case
  export let filter = [];
  export let update;

  let margin = {top: 30, right: 30, bottom: 60, left: 40}; // Adjust bottom for city labels
  let width = 360;
  let height = 200; // Adjusted for more vertical space
  let chartW = width - margin.left - margin.right;
  let chartH = height - margin.top - margin.bottom;

  let brushLayer;
  let xAxis;
  let yAxis;

  const brush = d3.brushX()
    .extent([[0, 0], [chartW, chartH]])
    .on("brush", brushed)
    .on("end", brushended);        

  function brushed(event) {
    if (event && event.selection) {
      const [min, max] = event.selection.map(xScale.invert);
      filter = counts
        .filter(d => xScale(d.label) >= min && xScale(d.label) <= max)
        .map(d => d.label);
      update();
    }
  }

  function brushended(event) {
    if (event && !event.selection) {
      filter = [];
      update();
    }
  }

  // Count occurrences for each city, sort, and take top 10
  $: counts = d3.rollups(data, v => v.length, d => d.properties.City)
    .map(([label, count]) => ({ label, count }))
    .sort((a, b) => d3.descending(a.count, b.count))
    .slice(0, 10); // Show only top 10 cities

  // Set up xScale for City labels
  $: xScale = d3.scaleBand()
    .domain(counts.map(d => d.label))
    .range([0, chartW])
    .padding(0.1);

  // Set up yScale based on the city counts
  $: yScale = d3.scaleLinear()
    .domain([0, d3.max(counts, d => d.count)])
    .range([chartH, 0]);

  // Render axes and brush
  $: {
    d3.select(brushLayer).call(brush);
    d3.select(xAxis).call(d3.axisBottom(xScale)).selectAll("text")
      .attr("transform", "rotate(-45)") // Rotate labels for readability
      .style("text-anchor", "end");
    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>

<main>
  <!-- Add title for the histogram to represent the city count -->
  <h3 style="text-align: center; color: white;">{variable} Count by Top 10 Cities</h3>
  
  <svg {width} {height}>
    <g transform="translate({margin.left}, {margin.top})">
      <!-- Bars representing top 10 city counts -->
      {#each counts as { label, count }}
        <rect class="bar"
          x={xScale(label)} 
          y={yScale(count)}
          width={xScale.bandwidth()}
          height={chartH - yScale(count)}
          on:click={() => handleClick(label)}/>
      {/each}
    </g>

    <!-- Brush and Axes Layers -->
    <g transform="translate({margin.left}, {margin.top})" bind:this={brushLayer} />
    <g transform="translate({margin.left}, {chartH + margin.top})" bind:this={xAxis} />
    <g transform="translate({margin.left}, {margin.top})" bind:this={yAxis} />
  </svg>
</main>

<style>
  .bar {
    fill: goldenrod;
    stroke: white;
    stroke-width: 1px;
  }

  /* Style for the title */
  h3 {
    margin: 0;
    font-size: 14px;
  }

  /* Rotate city labels */
  .x-axis text {
    font-size: 10px;
    transform: rotate(-45deg);
    text-anchor: end;
  }
</style>
