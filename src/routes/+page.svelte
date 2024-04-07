<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";
    import * as d3 from "d3";


    mapboxgl.accessToken = import.meta.env.VITE_MY_TOKEN;

    let houses = [];
    let map;
    let mapViewChanged = 0;

    async function geocodeAddress(address) {
    try {
      const response = await fetch(`https://api.mapbox.com/geocoding/v5/mapbox.places/${encodeURIComponent(address)}.json?access_token=${mapboxgl.accessToken}`);
      const data = await response.json();
      if (data.features && data.features.length > 0) {
        const coordinates = data.features[0].center;
        const lngLat = new mapboxgl.LngLat(coordinates[0], coordinates[1]);
        return lngLat
      } else {
        // Handle invalid address or no results
        console.error("No results found for the provided address");
      }
    } catch (error) {
      console.error("Error geocoding address:", error);
    }
  } 

    async function getCoords (house) {
        console.log("house:", house)
        let point = await geocodeAddress(house.full_address)
        // let point = new mapboxgl.LngLat(+house.Longitude, +house.Latitude);
        let {x, y} = map.project(point);
        return {cx: x, cy: y};
    }

    $: map?.on("move", evt => mapViewChanged++);

    onMount(async () => {
        map = new mapboxgl.Map({
            /* options */
            container: "map",
            style: "mapbox://styles/mapbox/streets-v12",
            center: [-71.3679725, 42.40010974514135],
            zoom: 10,
        });

        houses = await d3.csv("%sveltekit.assets%/filtered_boston_data.csv", residential => ({
            ...residential,
        }));
        console.log(houses)
    })


</script>

<h1>Zipcode Affordability</h1>

<div id="map">
    <svg>
        <!-- {#key mapViewChanged}
            {#each houses as house }
            <circle { ...getCoords(house) } r="5" fill="steelblue" />
            {/each}
        {/key} -->
    </svg>
</div>




<input type="number" id="income-box" placeholder="Enter your annual income">

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
        width: 20em;
        border-radius: 5px;
    }

</style>
