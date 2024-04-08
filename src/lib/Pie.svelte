<script>
    import * as d3 from 'd3';

    let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
    export let data = [];
    let colors = d3.scaleOrdinal(d3.schemeTableau10);
    let sliceGenerator = d3.pie().value(d => d.value);
    let arcData;
    let arcs;
    
    export let selectedIndex = -1;

    

    $: {
        arcData = sliceGenerator(data);
        arcs = arcData.map(d => arcGenerator(d));
    }
    


</script>

<svg viewBox="-50 -50 100 100">
	<circle cx="0" cy="0" r="50" fill="red" />
</svg>




<style>
svg {
	max-width: 20em;
	margin-block: 2em;
    height: 15em;
	/* Do not clip shapes outside the viewBox */
	overflow: visible;
} 


</style>

