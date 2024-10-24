<script>
    
    // use for county/us maps with AK and HI
    import us from '../data/counties-10m.json'; 
    import ban_data from '../data/bans.json';


    // use if you want just the upper 48
    // import us from '../data/counties-upper-49.json'; 

    //packages 
    import * as d3 from 'd3'; 
    import { onMount } from 'svelte'; 
    import * as topojson from 'topojson-client';
    import { isMobile, tooltipResults, tooltipHeight, tooltipWidth } from '../stores/global.js';

    export let data; 
    export let color_scale; 
    export let ban_color;
    //creates list of banned states
    let bans = ban_data.flatMap((d) => d.category == "Ban" ? [d.state] : []);
    //data[0] refers to the label, what the data was grouped by
    //data[1] contains the actual data array
    let label = data[0].replace(" ", "");
    let group_data = data[1];

    let second_timeperiod = "June2024";


    let aspect_ratio = 0.65;
    let width = 400;
    let height = width * aspect_ratio;
    //matches the fips code in the county topojson file to the county in the driving distance data to return the choropleth color scale
    function getCounty(fips) {
      let record = group_data.find((d) => d.fips_match == fips);
      return record ? color_scale(record.distance_origintodest) : "#e3e3e3";
  }


    // county map projection
    const counties = topojson.feature(us, us.objects.counties).features;
    const projection = d3.geoAlbersUsa()
        .scale(1300)
        .translate([487.5, 305])
        .fitSize([width, height], {type: "FeatureCollection", features: counties})
    // state borders, add a function as the third argument if you want only interior or exterior borders
    const mesh = topojson.mesh(us, us.objects.states);
    //creates a border for just the states with abortion bans by seeing whether the state name is in the list of banned states
    const banned_mesh = topojson.mesh(us, us.objects.states, (a,b) => bans.includes(a.properties.name) || bans.includes(b.properties.name));


    let path = d3.geoPath().projection(projection);

   

   


    onMount(() => {
    });


</script>

<div class="map-container">
    <svg
      viewbox="0 0 {width} {height}"
      style="width: 100%; height: 100%;"
      id={label}
      class="usSVG"
    >
  <!-- County choropleth fill -->
    <g class='counties'>
      {#each counties as county, i}
        <path d={path(county)}
          fill={getCounty(county.id)}
        />
      {/each}
    </g>
<!-- State borders -->
    <path class="border" d={path(mesh)}
    fill="none"
    stroke-linejoin="round"
    stroke="#FFF"
    stroke-width="0.5px"
  />
  <!-- Borders showing abortion bans only for the second map -->
  {#if label==second_timeperiod}
  <path class="border" d={path(banned_mesh)}
    fill="none"
    stroke-linejoin="round"
    stroke={ban_color}
    stroke-width="1.5px"
  />
  {/if}
 </svg>

     <!-- Anno, only for the second map -->
    {#if label==second_timeperiod}
    <div class="anno">The most affected counties in the southern U.S. would have to travel over <strong style="font-weight:800; color:#7a306c;">700 miles</strong> post-Dobbs</div>
    {/if}
</div>

  <style>

    .border {
      fill: none;
      stroke-linejoin: round;
    }

    .anno {
      text-align:center;
      width:60%;
      margin-left:20%;
      font-size: clamp(12px, 1.5vw, 14px);
    }
     
  </style>