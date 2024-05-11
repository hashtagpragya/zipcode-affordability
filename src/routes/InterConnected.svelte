<script>
  import mapboxgl from "mapbox-gl";
  import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let dataAffordable = [];
  let dataUnaffordable = [];
  let svg, linesMap;
  let householdIncome = 65000;
  let linesHouses = [];
  let linesMapViewChanged = 0;
  const customTimeFormat = d3.timeFormat("%Y");
  const years = Array.from({ length: 2023 - 2000 + 1 }, (_, index) =>
    (2000 + index).toString()
  );
  const bounds = [
    [-71.191421, 42.22788], // Southwest coordinates
    [-70.92087, 42.39694], // Northeast coordinates
  ];




  let width = 800,
    height = 500;
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
    rolledDataUnaffordable = [];
  const lineCoordinates = [
    [21.1818181818182, 370.0909090909091],
    [108.18181818181819, 350.0909090909091],
    [196.36363636363637, 365.1818181818182],
    [284.54545454545456, 300.25429666846406],
    [372.72727272727275, 260.3636363636364],
    [460.90909090909093, 270.4545454545455],
    [549.0909090909091, 230.3859297682076],
    [637.2727272727273, 170.63636363636363],
    [725.4545454545455, 163.72727272727275],
    [790.6363636363637, 161.75349091692405],
  ];
  mapboxgl.accessToken =
    "pk.eyJ1IjoiZWZhaXRoMSIsImEiOiJjbHVwM3hqbngxejEwMmlxcHZoMnd4NzVoIn0.aImOljzGu-9EUSa9aFcQzw";

  function translate(x, y) {
    return `translate(${x}, ${y})`;
  }

  onMount(async () => {
    linesMap = new mapboxgl.Map({
      container: "linesMap",
      style: "mapbox://styles/mapbox/light-v11",
      center: [-71.3679725, 42.40010974514135],
      zoom: 0,
      maxBounds: bounds,
    });

    await new Promise((resolve) => linesMap.on("load", resolve));

    linesHouses = await d3
      .csv(
        "https://raw.githubusercontent.com/efaith1/data_viz_python_scripts/main/filtered_boston_residential_sales.csv"
      )
      .then((linesHouses) => {
        return linesHouses;
      });

    xScale = d3
      .scaleTime()
      .domain([new Date(2000, 0), new Date(2023, 0)])
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

    function drawLineSegments() {
      d3.selectAll(".line-segment").remove();
      for (let i = 0; i < lineCoordinates.length - 1; i++) {
        const segment = [lineCoordinates[i], lineCoordinates[i + 1]];
        line = d3
          .select(svg)
          .append("path")
          .attr("d", d3.line()(segment))
          .attr("class", "line-segment")
          .transition()
          .delay(i * 1000)
          .duration(1000)
          .style("stroke", " #7469B6")
          .style("stroke-width", 10)
          .style("fill", "none");
      }
      setTimeout(drawLineSegments, (lineCoordinates.length - 1) * 1000);
    }

    drawLineSegments();

    function animateCircles() {
      setTimeout(() => {
        const circles = document.querySelectorAll(".animated-circle");
        circles.forEach((circle, index) => {
          setTimeout(() => {
            circle.setAttribute("r", "5");
          }, index * 2.2);
        });

        setTimeout(() => {
          circles.forEach((circle) => {
            circle.setAttribute("r", "0");
          });
        }, 6600);

        setTimeout(animateCircles, 8600);
      }, 0);
    }
    animateCircles();
  });

  $: filteredAffordableHouse = linesHouses.filter((house) =>
    affordable(house.first_year_payment, householdIncome)
  );

  $: filteredUnaffordableHouse = linesHouses.filter((house) =>
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

    filteredUnaffordableHouse.forEach((house) => {
      rolledDataUnaffordable.push([house.sale_year, house.first_year_payment]);
    });

    dataUnaffordable = rolledDataUnaffordable.map(
      ([first_year_payment, sale_year]) => {
        return { value: first_year_payment, label: sale_year };
      }
    );
  }

  $: linesMap?.on("move", (evt) => linesMapViewChanged++);

  function affordable(first_year_payment, income) {
    return income * 0.3 > first_year_payment;
  }

  function unaffordable(first_year_payment, income) {
    return income * 0.3 < first_year_payment;
  }

  function getCoords(house) {
    let point = new mapboxgl.LngLat(+house.lon, +house.lat);
    let { x, y } = linesMap.project(point);
    return { cx: x, cy: y };
  }
</script>
<div id="linesMap">
  <svg>
    {#key linesMapViewChanged}
        {#each years as year}
            {#each filteredUnaffordableHouse
                .slice(0, 3000)
                .filter((house) => house.sale_year === year) as house}
                <!-- White border -->
                <circle
                    {...getCoords(house)}
                    r="3"
                    fill="transparent"
                    class="animated-circle-border" 
                />
                <!-- Main circle -->
                <circle
                    {...getCoords(house)}
                    r="2" 
                    fill="#7469B6"
                    class="animated-circle"
                />
            {/each}
        {/each}
    {/key}
</svg>
</div>

<div id="lineGraph">
  <svg bind:this={svg}> </svg>
</div>

<style>
  @import url("$lib/global.css");

  @keyframes circleAnimation {
    0% {
      r: 0;
      opacity: 0;
    }
    100% {
      r: 5;
      opacity: 1;
    }
  }

  .animated-circle {
    animation: circleAnimation 0.5s ease;
  }

  .container {
    display: flex;
    align-items: center;
    gap: 2em;
    max-height: 250px;
  }

  svg {
    overflow: visible;
  }

  #lineGraph {
    display: flex;
    flex-direction: column;
    align-items: left;
    margin: 40px;
   
  }

  #lineGraph svg {
    position: absolute;
    z-index: 1;
    width: 800px;
    height: 500px;
    pointer-events: none;
  
  }
  .x-axis path,
  .x-axis line,
  .y-axis path,
  .y-axis line {
    fill: none;
    stroke: #000;
    stroke-width: 4;
    shape-rendering: crispEdges;
  }

  .x-axis text,
  .y-axis text {
    font-size: 12px;
  }

  #linesMap {
    flex: 1;
    overflow: auto;
    width: 800px;
    height: 500px;
  }

  #linesMap svg {
    position: absolute;
    z-index: 1;
    width: 100%;
    height: 100%;
    pointer-events: none;
  }
</style>
