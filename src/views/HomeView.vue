<script setup lang="ts">
import { onMounted, ref } from 'vue'
import axios from 'axios'
import type { Results } from '@/utils/types'
import ResultView from '@/views/ResultView.vue'
import { frontPageText } from '../utils/constants'

interface TreeTypes {
  ['string']: string
}

interface Tree {
  type: string
  amount: number
}

const treeTypes = ref<TreeTypes>()
const volume = ref('')
const currentTreeKey = ref('')
const selectedTrees = ref<Tree[]>([])
const results = ref<Results>()

onMounted(() => {
  fetchTypes()
})

const options = {
  method: 'GET',
  url: `${import.meta.env.VITE_API_BASE_URL}/types`,
  params: { language: 'en' },
  headers: { Authorization: import.meta.env.VITE_AUTH_HEADER }
}

async function fetchTypes() {
  axios
    .request(options)
    .then(function (response) {
      treeTypes.value = response.data.data
    })
    .catch(function (error) {
      console.error(error)
    })
}

// todo move to ResultView
function fetchResults() {
  const options = {
    method: 'POST',
    url: `${import.meta.env.VITE_API_BASE_URL}/calculate`,
    params: { language: 'nl', '': '' },
    headers: {
      Authorization: import.meta.env.VITE_AUTH_HEADER,
      'Content-Type': 'application/json',
      Accept: 'application/json'
    },
    data: {
      language: 'nl',
      wood_types: selectedTrees.value
    }
  }

  axios
    .request(options)
    .then(function (response) {
      console.log(response.data)
      results.value = response.data.data
      console.log(results.value)
    })
    .catch(function (error) {
      console.error(error)
    })
}

function addTree() {
  selectedTrees.value.push({ type: currentTreeKey.value, amount: parseInt(volume.value) })
  volume.value = ''
  currentTreeKey.value = ''
}

function calculate() {
  if (volume.value && currentTreeKey.value) {
    selectedTrees.value.push({ type: currentTreeKey.value, amount: parseInt(volume.value) })
  }
  fetchResults()
}
</script>

<template>
  <div id="hero">
    <header id="hero-text">CO2 Calculator</header>
  </div>

  <div id="wrapper">
    <div id="article">
      <h1>Bereken in 2 stappen hoeveel CO2 is vastgelegd in uw houtproducten</h1>
      {{ frontPageText }}
    </div>

    <div id="calculator">
      <select name="trees" v-model="currentTreeKey">
        <option value="" selected disabled hidden>Kies houtsoort</option>
        <option v-for="(item, key) in treeTypes" :value="key" v-bind:key="key">{{ item }}</option>
      </select>

      <input type="number" name="volume" v-model="volume" placeholder="Aantal m3 hout" />

      <button @click="addTree">Add</button>

      <h3 id="selected-trees-header" v-if="selectedTrees.length">Selected Trees</h3>
      <ul v-if="selectedTrees.length" class="tree-list">
        <li v-for="tree in selectedTrees" v-bind:key="tree.type" class="selected-trees-li">
          {{ tree.type }} {{ tree.amount }}
        </li>
      </ul>
      <button @click="calculate">Calculate</button>
    </div>
  </div>
  <ResultView :results="results" v-if="results" />
</template>

<style scoped>
#wrapper {
  width: 100%;
  max-width: 1280px;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  margin: auto auto;
}

#calculator {
  padding: 5px;
  display: flex;
  flex-direction: column;
  gap: 5px;
}

select {
  width: 100%;
}

button {
  border: 0;
  background: #50ae24;
  color: white;
  cursor: pointer;
  font-size: 18px;
  font-weight: 500;
  line-height: 1.7em;
  text-decoration: none;
}

#hero {
  background-image: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)),
    url('@/assets/trees.jpg');
  min-height: 200px;
  height: 50%;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  position: relative;
  display: flex;
  align-content: center;
}

#hero-text {
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: white;
  font-size: 5em;
}

#selected-trees-header {
  font-size: 1.5em;
  /*todo remove negative margin*/
  margin-bottom: -18px;
}

.selected-trees-li {
  display: flex;
  flex-direction: column;
}

.tree-list {
  list-style-type: none;
  padding-left: 0;
}
</style>
