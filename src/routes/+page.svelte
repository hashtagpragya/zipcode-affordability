<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";
    import { onMount } from "svelte";


    mapboxgl.accessToken = import.meta.env.VITE_MY_TOKEN;


    onMount(() => {
        let map = new mapboxgl.Map({
            /* options */
            container: "map",
            style: "mapbox://styles/mapbox/streets-v12",
            center: [-71.3679725, 42.40010974514135],
            zoom: 10,
        });
    })


</script>

<h1>Zipcode Affordability</h1>
<div id="map"/>

<input type="number" id="income-box" placeholder="Enter your annual income">

<style>
    @import url("$lib/global.css");
    #map {
	flex: 1;
    }

    #income-box{
        margin-top: 1em;
        padding: 0.5em;
        width: 20em;
        border-radius: 5px;
    }

</style>
