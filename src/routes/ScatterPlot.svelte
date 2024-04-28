<script>
    import * as d3 from 'd3';
    import {createEventDispatcher} from 'svelte';
    import {
        computePosition,
        autoPlacement,
        offset,
    } from '@floating-ui/dom';


    export let data = []
    const dispatch = createEventDispatcher();

    //Scales
    let xScale,yScale;
    let selectedHouses =[];
    
    let width = 1000, height = 800;
    let margin = {top: 10, right: 10, bottom: 30, left: 50};
    let usableArea = {
	top: margin.top,
	right: width - margin.right,
	bottom: height - margin.bottom,
	left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;


    $:xScale = d3.scaleLinear().domain([2000, 2023]).range([usableArea.left, usableArea.right])
        
    $: yScale = d3.scaleLinear().domain([0, 30000000]).range([usableArea.bottom, usableArea.top])
    
    let xAxis, yAxis;

    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(Math.floor(d/1000))+"k"));
    }

    // Adding tooltip
    let hoveredIndex = -1;
    let houseTooltip;
    let tooltipPosition = {x: 0, y: 0};
    $: hoveredHouse = data[hoveredIndex]??{};

    
    async function dotInteraction (index, evt) {
        let hoveredDot = evt.target;

        if (evt.type === "mouseenter" || evt.type === "focus") {
            hoveredIndex = index;
            tooltipPosition = await computePosition(hoveredDot, houseTooltip, {
                strategy: "fixed",
                middleware: [
                    offset(5), // spacing from tooltip to dot
                    autoPlacement()
                ],
            });

        }
        else if (evt.type === "mouseleave" || evt.type === "blur") {
            hoveredIndex = -1
        } else if (evt.type === "click" || evt.type === "keyup" && evt.key === "Enter") {
            selectedHouses = [data[index]]
        } 

    }

    // Brushing
    let svg;
    function brushed (evt) {
        let brushSelection = evt.selection;
        selectedHouses = !brushSelection ? [] : data.filter(house => {
            let min = {x: brushSelection[0][0], y: brushSelection[0][1]};
            let max = {x: brushSelection[1][0], y: brushSelection[1][1]};
            let x = xScale(house.year);
            let y = yScale(house.price);

            return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
        });
        dispatch('brushChanged', {detail: selectedHouses})
    }

    function isHouseSelected (house) {
	    return selectedHouses.includes(house);
    }

    $: {
	    d3.select(svg).call(d3.brush().on("start brush end", brushed));
        d3.select(svg).selectAll(".houses, .overlay ~ *").raise();
    }

</script>

<div class="wrapper">
    <svg viewBox="0 0 {width} {height}" bind:this={svg}>
        <g class="houses">
            {#each data as house, index}
                <circle
                    class:selected={isHouseSelected(house)}
                    cx={ xScale(house.year) }
                    cy={ yScale(house.price)}
                    r="5"
                    fill= "steelblue"
                    on:mouseenter={evt => dotInteraction(index, evt)}
                    on:mouseleave={evt => dotInteraction(index, evt)}
                    tabindex="0"
                    aria-describedby="housing-tooltip"
                    aria-haspopup="true"
                    on:click={evt => dotInteraction(index, evt)}
                />
            {/each}

            {#each selectedHouses as house}
                <circle
                cx={ xScale(house.year) }
                cy={ yScale(house.price)}
                r="5"
                />

            {/each}
        </g>
        <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
        <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
    </svg>
    <dl id="housing-tooltip" class="info tooltip" hidden={hoveredIndex === -1}  style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px"  bind:this={houseTooltip} role="tooltip">

        <dt>Style</dt>
        <dd>{ hoveredHouse.style }</dd>

        <dt>Price</dt>
        <dd>{hoveredHouse.price}</dd>
    
        <dt>Year_Sold</dt>
        <dd>{hoveredHouse.year }</dd>

        <dt>Year_Built</dt>
        <dd>{ hoveredHouse.yearbuilt }</dd>
    </dl>
    
</div>


<style>

#housing-tooltip {
    display: grid;
    grid-template-columns:  auto auto;
    transition-duration: 500ms;
    transition-property: opacity, visibility;
    
    &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
        }

}

.info dt{
    font-weight: bold;
}

svg {
    overflow: visible;
}

.wrapper {
    width: 100%;
    height: 100%;
}

.tooltip {
        position: fixed;
        top: 1em;
        left: 1em;
        background-color: rgb(232, 232, 232);
        box-shadow: 2px 2px 2px rgb(168, 168, 167);
        padding: 1em;
        border-radius: 10px;
    }

circle {
	transition: all 200ms;
    transform-origin: center;
    transform-box: fill-box;

	&:hover {
		transform: scale(1.5);
	}

    &.selected {
            fill: red;
        }

    @starting-style {
        r: 0;
    }
}

</style>