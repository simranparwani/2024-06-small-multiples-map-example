<script>
  import Window from "./components/Window.svelte";
  import Map from "./components/Map.svelte";
  import Legend from "./components/Legend.svelte";
  import data from "./data/data.json";

  import {group} from "d3-array";
  import {scaleThreshold} from "d3-scale";
  import pym from "pym.js";

  new pym.Child({
    polling: 500,
  });

  const altText = __ALT_TEXT__;
  let groupedData = Array.from(group(data, d=> d.label));
  
  // https://gka.github.io/palettes/#/8|s|e4e3b4,7a306c|ffffe0,ff005e,93003a|1|1
  let distance_range = [
    '#FFF', '#e4e3b4', '#d6c9aa', '#c7afa0', '#b89595', '#a97c8b', '#9a6381', '#8a4a76', '#7a306c'
  ];

  const distance_color = scaleThreshold()
    .domain([0, 50, 100, 150, 200, 250, 300, 350, 400])
    .range(distance_range);

  //color for banned state outlines
  const ban_color = "#484538";
</script>

<Window />
<div class="chart-container">
  <h1 class="headline">Driving distance to nearest abortion provider, by county</h1>
  <h1 class="dek">In miles; <div class="ban_legend" style:--ban={ban_color}></div> Ban on nearly all abortions</h1>
  <p class="sr-only">{altText}</p>
  <Legend color_scale={distance_color} />

  <div class="map-container">
    {#each groupedData as group}
      <div class="group">
        <p class="subheader">{group[0]}</p>
        <Map color_scale={distance_color} data={group} {ban_color} />
      </div>
    {/each}
  </div>
</div>

<style lang="scss">
  :global {
    @import "scss/style.scss";
  }

  .ban_legend {
    width:15px;
    height:15px;
    border: 2px solid var(--ban);
    margin-left:10px;
    margin-right:4px;
  }

  .subheader {
    text-align:center;
  }

  .dek {
    text-align: center;
    display:flex;
    justify-content: center;
    align-items: center;
  }
  
  .headline {
    text-align: center;
  }

  .map-container {
    margin-top:40px;
    display: grid;
    grid-template-columns: 1fr 1fr;
  }

@media only screen and (max-width: 599px) {
  .map-container {
    margin-top:25px;
    display: grid;
    grid-template-columns: 1fr;
  }
}
</style>
