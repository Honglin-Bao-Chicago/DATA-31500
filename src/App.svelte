<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import Map from './Map.svelte';
  import Histogram from './Histogram.svelte';

  let data = [];
  let fullData = [];
  let filterCity = [];
  let loading = true;

  onMount(async function() {
    let table = await d3.csv('illegal_animals_kept_as_pets_20241029.csv');
    let geocoord = await d3.json('Modified Zip Code Tabulation Areas (MODZCTA).geojson').then(d => d.features);

    await Promise.all([table, geocoord]).then((values) => {
      let table = values[0];
      let geocoord = values[1];

      for (let i = 0; i < geocoord.length; i++) {
        let modzcta = geocoord[i].properties.modzcta;
        let population = 0;
        let city = '';
        let borough = '';

        for (let j = 0; j < table.length; j++) {
          if (table[j]['Incident Zip'] === modzcta) {
            population += 1;
            city = table[j]['City'];
            borough = table[j]['Borough'];
          }
        }

        geocoord[i].properties['population'] = population;
        geocoord[i].properties['City'] = city;
        geocoord[i].properties['Borough'] = borough;

        data.push(geocoord[i]);
      }

      fullData = [...data];
      loading = false;
    });
  });

  function updateData() {
    if (filterCity.length > 0) {
      data = fullData.filter(d => filterCity.includes(d.properties.City));
    } else {
      data = [...fullData];
    }
  }
</script>

<main>
  <h1>Honglin Bao's Project NYC Illegal Animals Data by City</h1>

  <div class="flex-container row">
    {#if !loading}
      <div class="map"><Map {data} {fullData}></Map></div>
    {/if}
    <div class="flex-container col">
      <div class="hist"><Histogram {data} {fullData} variable="City" bind:filter={filterCity} update={updateData}></Histogram></div>
    </div>
  </div>
</main>

<style>
  .flex-container {
    display: flex;
    justify-content: center;
    height: 100%;
    padding: 15px;
    gap: 5px;
  }

  .flex-container > div {
    padding: 8px;
  }

  .flex-container .col {
    flex-direction: column;
  }

  .map { 
    flex-grow: 1;
  }

  .hist { 
    flex-grow: 0;
  }
</style>
