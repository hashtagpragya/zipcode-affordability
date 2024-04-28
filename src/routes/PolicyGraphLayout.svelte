<script>
    import * as d3 from 'd3';
    import {onMount} from "svelte";
    import ScatterPlot from './ScatterPlot.svelte';
    import PolicyGraph from './PolicyGraph.svelte';
    import HouseScale from './HouseScale.svelte';

    let data = [];
    let selectedHouses =[];
    let policy1Amount = 0;
    let policy2Amount = 0;

    function handleBrushingChanged(event) {
        selectedHouses = event.detail.detail;
    }

    $:{
        policy1Amount = d3.sum(selectedHouses, d => d.policy1)
        policy2Amount = d3.sum(selectedHouses, d => d.policy2)
    }

    $: housingpotential = Math.ceil((policy1Amount+policy2Amount)/500000)

    onMount(async() => {

        //Link to use: https://raw.githubusercontent.com/tranalex099/boston-housing-viz/main/filtered_policies_boston_residential_sales
        // field of interest:
        // year
        // yearbuilt
        // price
        // transfer_tax
        // property_tax

        data = await d3.csv("dummy_data.csv", house => ({
            ...house, 

            year: Number(house.year),
            price: Number(house.price),
            policy1: Number(house.policy1),
            policy2: Number(house.policy2),
            yearbuilt: Number(house.yearbuilt)

        }))
    })

</script>
<div class="container">
    <div class="scatterplot">
        <ScatterPlot on:brushChanged={handleBrushingChanged} data={data}/>
    </div>
    <div class="policy">
        <h2>Policies Effects</h2>
        <PolicyGraph policy1Amount={policy1Amount} policy2Amount={policy2Amount}/>

        <h2>The affordable housing potential</h2>
        
        <!-- TODO Do more meaningful calculations here -->
        <span> {housingpotential}</span>  
        <!-- TODO Add a scale visualization -->
        
    </div>
    
</div>


<style>
@import url("$lib/global.css");

.container {
    border: 1px solid black;
    width: 100%;
    display: grid;
    grid-template-columns: 2fr 1fr;
    column-gap: 50px;
    align-items: center;
}

.policy {
    display: flex;
    flex-direction: column;
    /* justify-content: center */

}

.policy h2{
    align-self: center;
    font-size: 30px;
}
span {
    font-size: 50px;
    font-weight: bold;
    align-self: center;
    border: 1px solid black;
    padding: 0.5em;
    border-radius: 20px;
}

</style>