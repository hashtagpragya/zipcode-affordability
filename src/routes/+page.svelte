<script>
  import mapboxgl from "mapbox-gl";
  import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import Pie from "$lib/Pie.svelte";
  import Grid from "svelte-grid-responsive";
  import InterConnected from "./InterConnected.svelte";

  let colors = d3.scaleOrdinal(d3.schemeTableau10);

  mapboxgl.accessToken =
    "pk.eyJ1IjoidXNvbmlhIiwiYSI6ImNsdW9xMnZlYjBpZmkya3BiODN4aHJmaDEifQ.7_EpwmnX-wcM1QNdRvujJg";

  let houses = [];
  let map, filteredHouse;
  let mapViewChanged = 0;
  let incomeFilter;
  const bounds = [
    [-71.191421, 42.22788], // Southwest coordinates
    [-70.92087, 42.39694], // Northeast coordinates
  ];

  // Make sure the variable definition is *outside* the block
  let data = [];

  $: {
    // Initialize to an empty object every time this runs
    data = [];

    // Calculate rolledData and pieData based on filteredProjects here
    let rolledData = d3.rollups(
      filteredHouse,
      (v) => v.length,
      (d) => d.style
    );
    data = rolledData.map(([style, count]) => {
      return { value: count, label: style };
    });
  }

  function getCoords(house) {
    let point = new mapboxgl.LngLat(+house.lon, +house.lat);
    let { x, y } = map.project(point);
    return { cx: x, cy: y };
  }

  function affordable(first_year_payment, income) {
    return income * 0.3 > first_year_payment;
  }

  $: map?.on("move", (evt) => mapViewChanged++);
  $: filteredHouse =
    incomeFilter == undefined
      ? houses
      : houses.filter((house) =>
          affordable(house.first_year_payment, incomeFilter)
        );
  onMount(async () => {
    map = new mapboxgl.Map({
      /* options */
      container: "map",
      style: "mapbox://styles/mapbox/streets-v12",
      center: [-71.3679725, 42.40010974514135],
      zoom: 10,
      maxBounds: bounds,
    });

    await new Promise((resolve) => map.on("load", resolve));
    map.on("load", function () {
      map.resize();
    });
    // map.setPaintProperty('background-patter', 'background-color', 'rgb(255, 0, 0)');

    houses = await d3
      .csv(
        "https://raw.githubusercontent.com/hashtagpragya/hashtagpragya.github.io/main/Smaller_dataset_1000.csv"
      )
      .then((houses) => {
        return houses;
      });
  });
</script>

<div class="header">
  <h2>Housing Affordability & Policy Implications</h2>
  <p>Team Robin Hood: Alex, Esther, Pragya, Sonia</p>
</div>
<h1>Data Comic</h1>
<div id="datacomic">
  <img src="datacomic1.jpeg" alt="datacomic_1" />
  <img src="datacomic_2.jpeg" alt="datacomic_2" />
</div>
<h1>InterConnected Map</h1>

<div id="InterconnectedMap">
  <Grid container gutter={12}>
    <Grid xs={12} lg={4} order={2}
      >Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
      tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
      veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
      commodo consequat. Duis aute irure dolor in reprehenderit in voluptate
      velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
      cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id
      est laborum.</Grid
    >
    <Grid xs={12} lg={4} order={2}>
      <div id="inter_map">
        <!-- <InterConnected/> -->
        <img
          src="interconnected_placeholder.png"
          alt="inter_placeholder"
          width="400"
          height="400"
        />
      </div>
    </Grid>
  </Grid>
</div>

<h1>Housing Affordability</h1>

<!-- <div id="map">
    <svg>
        {#key mapViewChanged}
            {#each filteredHouse as house, index }
            <circle { ...getCoords(house) } r="5" fill={ colors(index)} />
            {/each}
        {/key}
    </svg>
</div>

<div class="container">
    <input type="number" id="income-box" placeholder="Enter your annual income" bind:value={incomeFilter}>

    <ul class="legend">
        
        {#each data as d,index}
            <li style="--color: { colors(index) }">
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>   

</div> -->
<div id="HousingAffordability">
  <Grid container gutter={12}>
    <Grid xs={12} lg={4} order={2}
      >Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
      tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
      veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
      commodo consequat. Duis aute irure dolor in reprehenderit in voluptate
      velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
      cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id
      est laborum.</Grid
    >
    <Grid xs={12} lg={4} order={2}>
      <div id="map">
        <svg>
          {#key mapViewChanged}
            {#each filteredHouse as house, index}
              <circle {...getCoords(house)} r="5" fill={colors(index)} />
            {/each}
          {/key}
        </svg>
      </div>
      <div class="container">
        <input
          type="number"
          id="income-box"
          placeholder="Enter your annual income"
          bind:value={incomeFilter}
        />
        <ul class="legend">
          {#each data as d, index}
            <li style="--color: {colors(index)}">
              <span class="swatch"></span>
              {d.label} <em>({d.value})</em>
            </li>
          {/each}
        </ul>
      </div>
    </Grid>
  </Grid>
</div>

<h1>Policy</h1>

<div id="Policy">
  <Grid container gutter={12}>
    <Grid xs={12} lg={4} order={2}
      >Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
      tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
      veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
      commodo consequat. Duis aute irure dolor in reprehenderit in voluptate
      velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
      cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id
      est laborum.</Grid
    >
    <Grid xs={12} lg={4} order={2}>
      <div id="policy_viz">
        <img
          src="policy_placeholder.png"
          alt="policy"
          width="400"
          height="400"
        />
      </div>
    </Grid>
  </Grid>
</div>

<div class="footer">
  <p>Acknowledgements</p>
  <p>
    This project was developed with guidance and feedback from the <a
      href="https://www.mapc.org/"
      >Metropolitan Area Planning Commission (MAPC)</a
    > . This website is created as part of Massachusetts Institute of Technology's
    Interactive Data Visualization & Society (Spring 2024) coursework. We would like
    to express thanks to the instructors Arvind Satyanarayan and Catherine D'Ignazio
    as well as the course staff and our classmates for valuable comments and suggestions.
  </p>
</div>

<style>
  @import url("$lib/global.css");
  #map {
    flex: 1;
    width: 800px;
    height: 500px;
  }

  #map svg {
    position: relative;
    z-index: 1;
    pointer-events: none;
  }

  #income-box {
    margin-top: 1em;
    padding: 0.5em;
    width: 20em;
    border-radius: 5px;
    height: 2em;
    flex: 1;
  }

  ul {
    border: 1px solid black;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(100em, 1fr));
    flex: 1;
    background-color: white;
    max-width: 400px;
  }

  .container {
    display: flex;
    align-items: center;
    gap: 2em;
    margin: 10px;
    max-height: 250px;
  }

  .legend li {
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .legend li span {
    width: 15px;
    height: 15px;
    border-radius: 50%;
    background-color: var(--color);
  }

  .datacomic {
    width: 100%;
    display: block;
  }

  .header {
    background-color: #f3dfe8;
    text-align: center;
    padding: 20px;
    margin: 200;
  }

  .housingaffordability {
    width: 300px;
    height: 100px;
    padding: 50px;
  }
  .datacomic {
    width: 300px;
    height: 100px;
    padding: 50px;
  }

  .interconnectedmap {
    width: 300px;
    height: 100px;
    padding: 50px;
  }
  .policy {
    width: 300px;
    height: 100px;
    padding: 50px;
  }

  .policy_viz {
    width: 800px;
    height: 500px;
  }

  .inter_map {
    width: 800px;
    height: 500px;
  }
  .footer {
    background-color: #fff9d7;
    text-align: center;
    padding: 20px;
    margin: 200;
  }
</style>
