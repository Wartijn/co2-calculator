<script setup lang="ts">
import type { Results } from '@/utils/types'

defineProps<{
  results: Results
}>()
</script>
<template>
  <h1>Resultaten</h1>
  <!--  <div>Houtvolume van de producten </div>-->
  <div>
    De Europese bossen slaan deze hoeveelheid CO2 op in: {{ results?.wood_growth_per_second }}
  </div>
  <div>Vastgelegde CO2 in de houtproducten: {{ results?.co2 }}</div>
  <div>
    Vergelijking van netto-emissie van CO2 voor diverse materialen (KG):
    <ul>
      <!--      <li>hout </li>-->
      <li>beton: {{ results?.concrete }}</li>
      <li>pvc: {{ results?.pvc }}</li>
      <li>staal: {{ results?.steel }}</li>
      <li>aluminium {{ results?.aluminium }}</li>
    </ul>
  </div>
  <div>
    Uitstoot van {{ results?.emission_equivalent }} km aan uitlaatgassen van een middenklasse auto
  </div>
  <div>
    Elektriciteitsverbruik van {{ results?.electricity_consumption }} huishoudens in één jaar tijd
  </div>
</template>
