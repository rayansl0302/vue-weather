<template>
	<div v-for="city in savedCities" :key="city.id">
		<CityCard :city="city" @click="goToCotyView(city)" />
	</div>
	<p v-if="savedCities.length === 0" class="text-center">
		No locations added. To start tracking a location, search in the field above.
	</p>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import CityCard from './CityCard.vue'

const router = useRouter()

const savedCities = ref([])
const API_KEY = import.meta.env.VITE_OPEN_WEATHER_KEY

const getCities = async () => {
	if (localStorage.getItem('savedCities')) {
		savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
		const req = []
		savedCities.value.forEach((city) => {
			req.push(
				axios.get(
					`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${API_KEY}&units=metric`
				)
			)
		})

		const weatherData = await Promise.all(req)

		weatherData.forEach((value, index) => {
			savedCities.value[index].weather = value.data
		})
	}
}
await getCities()

const goToCotyView = (city) => {
	router.push({
		name: 'cityView',
		params: { state: city.state, city: city.city },
		query: { id: city.id, lat: city.coords.lat, lng: city.coords.lng },
	})
}
</script>
