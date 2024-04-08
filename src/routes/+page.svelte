<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";
    import * as d3 from "d3";


    mapboxgl.accessToken = import.meta.env.VITE_MY_TOKEN;

    let houses = [];
    let map, filteredHouse;
    let mapViewChanged = 0;
    let incomeFilter = -1;

    function getCoords (house) {
        let point = new mapboxgl.LngLat(+house.lon, +house.lat);
        let {x, y} = map.project(point);
        return {cx: x, cy: y};
    }

    function affordable (first_year_payment, income) {
        return income*0.3 > first_year_payment
    }


    $: map?.on("move", evt => mapViewChanged++);
    $: filteredHouse = incomeFilter === -1? houses: houses.filter(house => affordable(house.first_year_payment, incomeFilter));
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
            {#each filteredHouse as house }
            <circle { ...getCoords(house) } r="5" fill="steelblue" />
            {/each}
        {/key}
    </svg>
</div>




<input type="number" id="income-box" placeholder="Enter your annual income" bind:value={incomeFilter}>

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
    /* background-color: aqua; */
    }

    #income-box{
        margin-top: 1em;
        padding: 0.5em;
        width: 20em;
        border-radius: 5px;
    }

</style>
