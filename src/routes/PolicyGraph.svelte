<script>
    import * as d3 from "d3";
    
    export let policy1Amount = 0;
    export let policy2Amount = 0;

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
    usableArea.width = (policy1Amount==0 && policy2Amount==0)? 1: usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;


    console.log(usableArea.width)
    
    $:xScale = d3.scaleLinear().domain([0, d3.max([policy1Amount, policy2Amount])]).range([0, usableArea.width]);
        
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
          Policy 1
          </text>
          <rect
            x={0}
                      y={yScale("Policy 1")}
                      width={xScale(policy1Amount)}
            height={yScale.bandwidth()}
          />
          <text
            text-anchor="start"
            x={xScale(policy1Amount)}
            dx="2"
            y={yScale("Policy 1") + yScale.bandwidth() / 2}
            dy=".35em"
          >
            {formatLabel(policy1Amount)}
          </text>

              <!-- Policy 2 -->
              <text
                text-anchor="end"
                x={-2}
                y={yScale("Policy 2") + yScale.bandwidth() / 2}
                dy=".35em"
              >
              Policy 2
              </text>
              <rect
                x={0}
                          y={yScale("Policy 2")}
                          width={xScale(policy2Amount)}
                height={yScale.bandwidth()}
              />
              <text
                text-anchor="start"
                x={xScale(policy2Amount)}
                dx="2"
                y={yScale("Policy 2") + yScale.bandwidth() / 2}
                dy=".35em"
              >
                {formatLabel(policy2Amount)}
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