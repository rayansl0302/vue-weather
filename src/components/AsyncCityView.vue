<template>
	<div class="flex flex-col flex-1 items-center">
		<!-- Banner -->
		<div
			v-if="route.query.preview"
			class="text-white p-4 bg-weather-secondary w-full text-center"
		>
			<p>
				You are currently previewing this city, click the "+" icon to add this
				city to your list.
			</p>
		</div>
		<!-- Weather Overview -->
		<div class="flex flex-col items-center text-white py-12">
			<h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
			<p class="text-sm">
				{{
					new Date().toLocaleDateString('sv-SE', {
						weekday: 'long',
						day: '2-digit',
						month: 'short',
					})
				}}
				{{
					new Date(weatherData.dt * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
			<p class="text-8xl">{{ Math.round(weatherData.main.temp) }}&deg;c</p>
			<img
				class="w-[150px] h-auto"
				:src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
				:alt="`${weatherData.weather[0].description}`"
				:title="`${weatherData.weather[0].description}`"
			/>
			<p class="text-xl">
				Känns som
				{{ Math.round(weatherData.main.feels_like) }}&deg;c
			</p>
			<p>
				L {{ Math.round(weatherData.main.temp_min) }}&deg;c / H
				{{ Math.round(weatherData.main.temp_max) }}&deg;c
			</p>
			<p class="capitalize">
				{{ weatherData.weather[0].description }}
			</p>
		</div>
	</div>
	<hr class="border-white border-opacity-10 border w-full" />
	<div class="flex flex-col items-center text-white py-12"></div>
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
