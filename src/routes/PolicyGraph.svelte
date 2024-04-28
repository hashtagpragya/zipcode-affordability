<script>
    import * as d3 from "d3";
    
    export let property_taxAmount = 0;
    export let transfer_taxAmount = 0;

    export let houseCounts;
    const formatLabel = d3.format(',.0f');


    let width= 500, height = 300;
    let margin = {top: 10, right: 60, bottom: 30, left: 50};

    let usableArea = {
	top: margin.top,
	right: width - margin.right,
	bottom: height - margin.bottom,
	left: margin.left
    };
    usableArea.width = (property_taxAmount==0 && transfer_taxAmount==0)? 1: usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;


    console.log(usableArea.width)
    
    $:xScale = d3.scaleLinear().domain([0, d3.max([property_taxAmount, transfer_taxAmount])]).range([0, usableArea.width]);
        
    $: yScale = d3.scaleBand().domain(["Policy 1", "Policy 2"]).range([usableArea.height, 0]).padding(0.25); // Replace these with actual policy names

</script>

<div class="wrapper">
    <svg viewBox="0 0 {width} {height}">
        <g transform={`translate(${margin.left}, ${margin.top})`}>

                                    
            <!-- Policy 1 -->
            <text
            text-anchor="end"
            x={-2}
            y={yScale("Policy 1") + yScale.bandwidth() / 2}
            dy=".35em"
          >
          Property
          </text>
          <rect
            x={0}
                      y={yScale("Policy 1")}
                      width={xScale(property_taxAmount)}
            height={yScale.bandwidth()}
          />
          <text
            text-anchor="start"
            x={xScale(property_taxAmount)}
            dx="2"
            y={yScale("Policy 1") + yScale.bandwidth() / 2}
            dy=".35em"
          >
            {formatLabel(property_taxAmount)}
          </text>

              <!-- Policy 2 -->
              <text
                text-anchor="end"
                x={-2}
                y={yScale("Policy 2") + yScale.bandwidth() / 2}
                dy=".35em"
              >
              Transfer
              </text>
              <rect
                x={0}
                          y={yScale("Policy 2")}
                          width={xScale(transfer_taxAmount)}
                height={yScale.bandwidth()}
              />
              <text
                text-anchor="start"
                x={xScale(transfer_taxAmount)}
                dx="2"
                y={yScale("Policy 2") + yScale.bandwidth() / 2}
                dy=".35em"
              >
                {formatLabel(transfer_taxAmount)}
              </text>




          </g>
    </svg>
</div>

<style>

  text {
    font-size: 12px;
  }

rect {
    fill: #4427ca;
  }
</style>