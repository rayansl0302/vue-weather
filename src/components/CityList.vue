<template>
	<div v-for="city in savedCities" :key="city.id">
		<CityCard :city="city" @click="goToCotyView(city)" />
	</div>
	<p v-if="savedCities.length === 0" class="text-center">
		<span class="block">Inga platser tillagda.</span> För att spara en plats,
		sök i fältet ovan.
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
		await new Promise((res) => setTimeout(res, 500))

		weatherData.forEach((value, index) => {
			savedCities.value[index].weather = value.data
		})

		savedCities.value.forEach((item) => {
			if (item.weather.rain) {
				item.weather.rainAmount = Object.values(item.weather.rain)[0]
			}
			if (item.weather.snow) {
				item.weather.snowAmount = Object.values(item.weather.snow)[0]
			}
		})

		console.log(savedCities.value)
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
