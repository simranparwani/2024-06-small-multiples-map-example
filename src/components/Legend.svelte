<script>
    import { onMount } from 'svelte'; 
    import { isMobile } from '../stores/global.js';
    export let color_scale; 
    let legend_items = color_scale.domain().slice(1);
    let width = $isMobile ? "30px" : "40px";
</script>

<div class="legend">
  {#each legend_items as d,i}
    <span class="item">
      <span class="block" style={"width: " + width + "; background-color: " + color_scale.range()[i+1] }></span> 
      <span style="width: 1px; height: 20px; background-color: rgb(34, 34, 34); position: relative;">
        <span class="text" style="position: absolute; top: 27px; left: 50%; transform: translate(-50%, -50%); color: rgb(101, 101, 104); font-size: 12px;">
          {i === legend_items.length -1 ? d + "+" : d}
        </span>
      </span>
    </span>
  {/each}
</div>

<style>
  .block {
    height: 14px; 
  }

  .legend {
    display: flex;
    flex-direction: row;
    justify-content: center;  
  }

  .item {
    display:flex;
    justify-content:center;
  }
</style>