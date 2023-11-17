<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQurey"
        @input="getSearchResults"
        placeholder="Search for a city!"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        v-if="mapBoxSearchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
        <li
          v-for="searchResults in mapBoxSearchResults"
          :key="searchResults.id"
          class="py-2 cursor-pointer"
        >
          {{ searchResults.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
const mapBoxAPIKey =
  'pk.eyJ1IjoiYWp2YXJyZWxpc2giLCJhIjoiY2xwMzVucnVjMHpzNDJobmxhZG1yMG1nNSJ9.PaX71_k3j27gRifYO9OfHw'

const searchQurey = ref('')
const queryTimeout = ref(null)
const mapBoxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQurey.value !== '') {
      const res = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQurey.value}.json?access_token=${mapBoxAPIKey}&types=place`
      )
      mapBoxSearchResults.value = res.data.features
      console.log(mapBoxSearchResults.value)
      return
    }
    mapBoxSearchResults.value = null
  }, 300)
}
</script>
