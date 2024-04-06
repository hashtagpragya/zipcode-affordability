<script>
    import mapboxgl from "mapbox-gl";
    import "../../node_modules/mapbox-gl/dist/mapbox-gl.css";

    mapboxgl.accessToken = import.meta.env.VITE_MY_TOKEN;

</script>

<h1>Zipcode Affordability</h1>
<div id="map"/>

<input type="number" id="income-box" placeholder="Enter your annual income">

<style>
    @import url("$lib/global.css");
    #map {
	flex: 1;
    background-color: aqua;
    }

    #income-box{
        margin-top: 1em;
        padding: 0.5em;
        width: 20em;
        border-radius: 5px;
    }

</style>
