<script>
    import { onMount } from 'svelte';
    import { select } from 'd3-selection';
    import { arc } from 'd3-shape';
    import { scaleLinear } from 'd3-scale';
  
    let value = 50;
  </script>
  
  <input type="range" id="points" name="points" min="0" max="10">

  
  <style>
  
input {
    
}
  </style>
  