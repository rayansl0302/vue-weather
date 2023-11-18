<template>
	<div></div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

const route = useRoute()
const API_KEY = import.meta.env.VITE_OPEN_WEATHER_KEY

const getWeatherData = async () => {
	try {
		const weatherData = await axios.get(
			`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}&units=metric`
		)
		return weatherData.data
	} catch (err) {
		console.error('ERROR::', err)
	}
}

const weatherData = await getWeatherData()
console.log(weatherData)
</script>
