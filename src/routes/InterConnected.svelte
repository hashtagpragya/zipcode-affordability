<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";
    import * as d3 from "d3";
  
    let dataAffordable = [];
    let dataUnaffordable = [];
    let svg, map;
    let householdIncome = 65000;
    let houses = [];
    let mapViewChanged = 0;
    const customTimeFormat = d3.timeFormat("%Y");
    let affordableHouseCostChange;
    const years = Array.from({ length: 2023 - 2000 + 1 }, (_, index) =>
      (2000 + index).toString()
    );
  
    let width = 1000,
      height = 600;
    let margin = { top: 10, right: 10, bottom: 30, left: 20 };
    let usableArea = {
      top: margin.top,
      right: width - margin.right,
      bottom: height - margin.bottom,
      left: margin.left,
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
  
    let xScale, yScale, xAxis, yAxis, line;
    let filteredAffordableHouse,
      filteredUnaffordableHouse,
      rolledDataAffordable,
      rolledDataUnaffordable = [];
    mapboxgl.accessToken =
      "pk.eyJ1IjoidXNvbmlhIiwiYSI6ImNsdW9xMnZlYjBpZmkya3BiODN4aHJmaDEifQ.7_EpwmnX-wcM1QNdRvujJg";
  
    // Animate
    // AVERAGE cost of those affordable styles by year: data map - FIRST YEAR PAYMENT BY YEAR
  
    onMount(async () => {
      map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/streets-v12",
        center: [-71.3679725, 42.40010974514135],
        zoom: 10,
      });
  
      await new Promise((resolve) => map.on("load", resolve));
  
      houses = await d3
        .csv(
          "https://raw.githubusercontent.com/efaith1/data_viz_python_scripts/main/filtered_boston_residential_sales.csv"
        )
        .then((houses) => {
          return houses;
        });
  
      xScale = d3
        .scaleTime()
        .domain([new Date(2000, 0), new Date(2024, 0)])
        .range([usableArea.left, usableArea.right]);
  
      yScale = d3
        .scaleLinear()
        .domain([0, 20000])
        .range([usableArea.bottom, usableArea.top]);
  
      xAxis = d3
        .select(svg)
        .append("g")
        .attr("class", "x-axis")
        .attr("transform", translate(0, usableArea.bottom))
        .call(
          d3
            .axisBottom(xScale)
            .tickFormat((d) => customTimeFormat(d).replace(/,/g, ""))
        );
      yAxis = d3
        .select(svg)
        .append("g")
        .attr("class", "y-axis")
        .attr("transform", translate(usableArea.left, 0))
        .call(d3.axisLeft(yScale));
  
      // Define the number of points on the line
      const numPoints = 12;
  
      // Calculate the increment for x and y values
      const xIncrement = (usableArea.right - usableArea.left) / (numPoints - 1);
      const yIncrement = (usableArea.top - usableArea.bottom) / (numPoints - 1);
  
      const first = [
        [usableArea.left, usableArea.bottom],
        [usableArea.right, usableArea.top],
      ][0][0];
      const second = [
        [usableArea.left, usableArea.bottom],
        [usableArea.right, usableArea.top],
      ][0][1];
      // Initialize line coordinates array
      const lineCoordinates = [
        // [first, second],
        // [first, second],
        // [first, second],
        // [first, second],
      ];
  
      // Generate line coordinates with bumps
      for (let i = 0; i < numPoints; i++) {
        const x = usableArea.left + i * xIncrement;
        let y = usableArea.bottom + i * yIncrement;
  
        // Add bumps
        if (i % 3 === 0) {
          // Bump every 3rd point
          y += Math.random() * 100; // Add random value to y
        }
  
        // Add point to lineCoordinates array
        lineCoordinates.push([x, y]);
      }
  
      // Append the line to the SVG
      const line = d3
        .select(svg)
        .append("path")
        .attr("d", d3.line()(lineCoordinates)) // Generate path string from line coordinates
        .style("stroke", "steelblue")
        .style("stroke-width", 2)
        .style("fill", "none");
  
      // const lineCoordinates = [
      //   [usableArea.left, usableArea.bottom],
      //   [usableArea.right, usableArea.top],
      // ];
  
      //   line = d3
      //     .select(svg)
      //     .append("line")
      //     .attr("x1", lineCoordinates[0][0])
      //     .attr("y1", lineCoordinates[0][1])
      //     .attr("x2", lineCoordinates[1][0])
      //     .attr("y2", lineCoordinates[1][1])
      //     .style("stroke", "steelblue")
      //     .style("stroke-width", 2);
    });
  
    $: filteredAffordableHouse = houses.filter((house) =>
      affordable(house.first_year_payment, householdIncome)
    );
  
    $: filteredUnaffordableHouse = houses.filter((house) =>
      unaffordable(house.first_year_payment, householdIncome)
    );
  
    $: {
      let rolledDataAffordable = d3.rollups(
        filteredAffordableHouse,
        (v) => v.sale_year,
        (d) => d.first_year_payment
      );
  
      rolledDataAffordable = []; // workaround
  
      filteredAffordableHouse.forEach((house) => {
        rolledDataAffordable.push([house.sale_year, house.first_year_payment]);
      });
  
      dataAffordable = rolledDataAffordable.map(
        ([first_year_payment, sale_year]) => {
          return { value: first_year_payment, label: sale_year };
        }
      );
  
      for (let i = 2000; i < 2024; i++) {
        //   let total = 0;
        for (let [year, price] of dataAffordable.entries()) {
          // let year = house.value;
          // let price = house.label;
          if (year.value === i) {
            // total += price;
          }
        }
      }
  
      filteredUnaffordableHouse.forEach((house) => {
        rolledDataUnaffordable.push([house.sale_year, house.first_year_payment]);
      });
  
      dataUnaffordable = rolledDataUnaffordable.map(
        ([first_year_payment, sale_year]) => {
          return { value: first_year_payment, label: sale_year };
        }
      );
    }
  
    $: map?.on("move", (evt) => mapViewChanged++);
  
    function affordable(first_year_payment, income) {
      return income * 0.3 > first_year_payment;
    }
  
    function unaffordable(first_year_payment, income) {
      return income * 0.3 < first_year_payment;
    }
  
    function getCoords(house) {
      let point = new mapboxgl.LngLat(+house.lon, +house.lat);
      let { x, y } = map.project(point);
      return { cx: x, cy: y };
    }
  </script>
  
  <h1>Map and Line for 65000/yr household income</h1>
  
  <!-- animate slow -->
  <div id="map">
    <svg>
      {#key mapViewChanged}
        {#each years as year}
          {#each filteredUnaffordableHouse
            .slice(0, 1000)
            .filter((house) => house.sale_year === year) as house}
            <circle
              {...getCoords(house)}
              r="5"
              fill="green"
              class="animated-circle"
            />
          {/each}
        {/each}
      {/key}
    </svg>
  </div>
  
  <div id="lineGraph">
    <svg viewBox={`0 0 ${width} ${height}`} bind:this={svg}> </svg>
  </div>
  
  <style>
    @import url("$lib/global.css");
    #income-box {
      margin-top: 1em;
      padding: 0.5em;
      width: 21em;
      border-radius: 5px;
      height: 2em;
    }
  
    .container {
      display: flex;
      align-items: center;
      gap: 2em;
      margin: 40px;
      max-height: 250px;
    }
  
    svg {
      overflow: visible;
    }
  
    #lineGraph {
      display: flex;
      flex-direction: column;
      align-items: left;
      padding-left: 50px;
      margin: 40px;
    }
  
    #lineGraph svg {
      position: absolute;
      z-index: 1;
      width: 100%;
      height: 100%;
      pointer-events: none;
    }
    .x-axis path,
    .x-axis line,
    .y-axis path,
    .y-axis line {
      fill: none;
      stroke: #000;
      shape-rendering: crispEdges;
    }
  
    .x-axis text,
    .y-axis text {
      font-size: 12px;
    }
  
    #map {
      flex: 1;
      overflow: auto;
    }
  
    #map svg {
      position: absolute;
      z-index: 1;
      width: 100%;
      height: 100%;
      pointer-events: none;
    }
  </style>