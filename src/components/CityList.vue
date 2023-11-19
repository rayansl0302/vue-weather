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

		await Promise.all(
			savedCities.value.map(async (city) => {
				const weatherResponse = await axios.get(
					`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${API_KEY}&units=metric`
				)

				city.weather = weatherResponse.data

				const cityName = city.weather.name.replace(/\s+/g, '-').toLowerCase()

				try {
					const teleportResponse = await axios.get(
						`https://api.teleport.org/api/urban_areas/slug:${cityName}/images/`
					)
					city.image =
						teleportResponse.data.photos.length > 0
							? teleportResponse.data.photos[0].image
							: 'URL_TO_FALLBACK_IMAGE'
				} catch (error) {
					console.info(`Can't find a image for ${cityName}`)
					city.image = ''
				}
			})
		)
		savedCities.value.forEach((item) => {
			if (item.weather.rain) {
				item.weather.rainAmount = Object.values(item.weather.rain)[0]
			}
			if (item.weather.snow) {
				item.weather.snowAmount = Object.values(item.weather.snow)[0]
			}
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
