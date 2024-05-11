<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";
    import * as d3 from "d3";
    import Pie from '$lib/Pie.svelte';


    let colors = d3.scaleOrdinal(d3.schemeTableau10);
    let colorDict = {"Single-Family Homes": " #AD88C6", "Condominiums/Apartments": "#FB9AD1", "Other": "#BBC3A4", "Multi-Family Dwellings": "#6196A6",
                     "Row Houses": "#f7a072", "Contemporary": "#9a8c98"}

    mapboxgl.accessToken = "pk.eyJ1IjoidXNvbmlhIiwiYSI6ImNsdW9xMnZlYjBpZmkya3BiODN4aHJmaDEifQ.7_EpwmnX-wcM1QNdRvujJg";

    let houses = [];
    let map, filteredHouse;
    let mapViewChanged = 0;
    let incomeFilter;
//     const bounds = [
//     [-73.5023, 41.1865], // Southwest coordinates
//     [-70.8157, 42.8864]  // Northeast coordinates

//   ];

const centerLatitude = 42.3960562;
const centerLongitude = -71.0878346;


// Define a buffer distance in degrees (adjust as needed)
const bufferDistance = 0.5; // Adjust as needed

// Calculate bounds based on the buffer distance
const southwest = [centerLongitude - bufferDistance, centerLatitude - bufferDistance];
const northeast = [centerLongitude + bufferDistance, centerLatitude + bufferDistance];

const bounds = [southwest, northeast];


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
            style: "mapbox://styles/mapbox/light-v11",
            center: [ -71.086238,   42.305997],
            zoom: 11,
            minZoom: 10.5,
            maxBounds: bounds

        });

        await new Promise(resolve => map.on("load", resolve));
        
        houses = await d3.csv("https://raw.githubusercontent.com/usonia09/zipcode-affordability/main/data/Smaller_dataset_1000.csv").then(houses => {
            return houses;
        });

    })


</script>


<div id="map">
    <svg>
        {#key mapViewChanged}
            {#each filteredHouse as house, index }
            <circle { ...getCoords(house) } r="6" fill="white" />
            <circle { ...getCoords(house) } r="5" fill={colorDict[house.style]} />
            {/each}
        {/key}
    </svg>
</div>


<div class="container">
    <input type="number" id="income-box" placeholder="Enter your annual income" bind:value={incomeFilter}>

    <ul class="legend">
    
        {#each data as d,index}
            <li style="--color: { colorDict[d.label] }">
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
    width: 100%;
    height: 500px;
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

    .container {
        padding: 10px;
        display: flex;
        gap: 10px;
    }

        
    ul {
        display: grid;
        grid-template-columns: auto auto;
        column-gap: 30px;
        background-color: #fff9d7;
        border-radius: 10px;

    }

    input {
        border: 1px solid grey;
        background-color: white;
        width: 100px;
    }

    .legend li {
        display: flex;   
        align-items: center;
        gap: 5px;
    }

    .legend li span {
        display: inline-block;
        width: 15px;
        height: 15px;
        border-radius: 50%;
        background-color: var(--color);

    }



</style>