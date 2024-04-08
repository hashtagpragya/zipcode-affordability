<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";
    import * as d3 from "d3";
    import Pie from '$lib/Pie.svelte';


    let colors = d3.scaleOrdinal(d3.schemeTableau10);

    mapboxgl.accessToken = import.meta.env.VITE_MY_TOKEN;

    let houses = [];
    let map, filteredHouse;
    let mapViewChanged = 0;
    let incomeFilter;


     // Make sure the variable definition is *outside* the block
     let data = [];


    $: {
    // Initialize to an empty object every time this runs
    data = [];


    // Calculate rolledData and pieData based on filteredProjects here
    let rolledData = d3.rollups(filteredHouse, v => v.length, d => d.style);
    data = rolledData.map(([style, count]) => {
        return { value: count, label: style };
        });
    }

    function getCoords (house) {
        let point = new mapboxgl.LngLat(+house.lon, +house.lat);
        let {x, y} = map.project(point);
        return {cx: x, cy: y};
    }

    function affordable (first_year_payment, income) {
        return income*0.3 > first_year_payment
    }


    $: map?.on("move", evt => mapViewChanged++);
    $: filteredHouse = incomeFilter == undefined? houses: houses.filter(house => affordable(house.first_year_payment, incomeFilter));
    onMount(async () => {
        map = new mapboxgl.Map({
            /* options */
            container: "map",
            style: "mapbox://styles/mapbox/streets-v12",
            center: [-71.3679725, 42.40010974514135],
            zoom: 10,
        });

        await new Promise(resolve => map.on("load", resolve));
        
        houses = await d3.csv("https://raw.githubusercontent.com/usonia09/zipcode-affordability/main/data/Smaller_dataset_1000.csv").then(houses => {
            return houses;
        });

    })


</script>

<h1>Zipcode Affordability</h1>

<div id="map">
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

</div>






<style>
    @import url("$lib/global.css");
    #map {
	flex: 1;
    }

    #map svg {
    position: absolute;
    z-index: 1;
    width: 100%;
    height: 100%;
    pointer-events: none;
    }

    #income-box{
        margin-top: 1em;
        padding: 0.5em;
        width: 21em;
        border-radius: 5px;
        height: 2em;
    }

        
    ul {
        border: 1px solid rgb(233, 229, 229);
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(11em, 1fr));
        flex: 1;

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



</style>
