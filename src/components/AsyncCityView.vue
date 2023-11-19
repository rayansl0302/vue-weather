<template>
	<div class="flex flex-col flex-1 items-center">
		<!-- Banner -->
		<div
			v-if="route.query.preview"
			class="text-white p-4 bg-weather-secondary w-full text-center"
		>
			<p>Klicka på "+"-ikonen för att lägga till denna stad i din lista.</p>
		</div>
		<!-- Weather Overview -->
		<div class="flex flex-col items-center text-white py-4">
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
			<p>
				<i class="fa-solid fa-wind"></i> {{ weatherData.wind.speed }} m/s
				<i
					:style="{ transform: `rotate(${weatherData.wind.deg}deg)` }"
					class="fa-solid fa-angles-down"
				></i>
			</p>
			<p>
				<i class="fa-regular fa-sun"></i>
				{{
					new Date(weatherData.sys.sunrise * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
			<p>
				<i class="fa-regular fa-moon"></i>
				{{
					new Date(weatherData.sys.sunset * 1000).toLocaleTimeString('sv-SE', {
						timeStyle: 'short',
					})
				}}
			</p>
		</div>
		<hr class="border-white border-opacity-10 border w-full mb-4" />
		<!-- Hourly Weather -->
		<div class="max-w-screen-md w-full py-4 mx-auto">
			<div class="mx-8 text-white">
				<h2 class="mb-4 text-lg font-bold">Kommande dygn</h2>
				<div class="flex gap-10 overflow-x-scroll md:justify-center py-4">
					<div
						v-for="hourData in forecastData.list.slice(0, 9)"
						:key="hourData.dt"
						class="flex flex-col gap-4 items-center"
					>
						<p class="whitespace-nowrap text-md capitalize">
							{{
								new Date(hourData.dt_txt).toLocaleTimeString('sv-SE', {
									weekday: 'long',
									hour: 'numeric',
									minute: 'numeric',
								})
							}}
						</p>
						<img
							class="w-auto h-[50px] object-cover"
							:src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
							alt=""
						/>
						<p class="text-xl">{{ Math.round(hourData.main.temp) }}&deg;c</p>
						<p class="text-xs text-center">
							<span class="block"
								>H &nbsp; {{ Math.round(hourData.main.temp_max) }}&deg;c</span
							>
							<span class="block"
								>L &nbsp; {{ Math.round(hourData.main.temp_min) }}&deg;c
							</span>
						</p>
					</div>
				</div>
			</div>
		</div>
		<hr class="border-white border-opacity-10 border w-full mb-4" />
		<!-- Weekly Weather -->
		<div class="max-w-screen-md w-full py-4">
			<div class="mx-8 text-white">
				<h2 class="mb-4 text-lg font-bold">Kommande vecka</h2>
				<div class="flex gap-10 overflow-x-scroll md:justify-center py-4">
					<div
						v-for="daily in dailyData"
						:key="daily.dt"
						class="flex flex-col gap-4 items-center"
					>
						<p class="whitespace-nowrap text-md capitalize">
							{{
								new Date(daily.dt_txt.replace(/-/g, '/')).toLocaleDateString(
									'sv-SE',
									{
										weekday: 'long',
									}
								)
							}}
						</p>
						<img
							class="w-auto h-[50px] object-cover"
							:src="`http://openweathermap.org/img/wn/${daily.weather[0].icon}@2x.png`"
							alt=""
						/>
						<p class="text-xl">{{ Math.round(daily.main.temp) }}&deg;c</p>
						<p class="text-xs text-center">
							<span class="block"
								>H &nbsp; {{ Math.round(daily.main.temp_max) }}&deg;c</span
							>
							<span class="block"
								>L &nbsp; {{ Math.round(daily.main.temp_min) }}&deg;c
							</span>
						</p>
					</div>
				</div>
			</div>
		</div>
	</div>

	<hr class="border-white border-opacity-10 border w-full mb-4" />

	<div
		v-if="!route.query.preview"
		class="flex items-center justify-center gap-2 py-8 text-white cursor-pointer duration-150 hover:text-red-500"
		@click="removeCity"
	>
		<i class="fa-solid fa-trash"></i>
		<p>Ta bort stad</p>
	</div>
</template>

<script setup>
import axios from 'axios'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const API_KEY = import.meta.env.VITE_OPEN_WEATHER_KEY
const BASE_URL = 'https://api.openweathermap.org/data/2.5/'

const getWeatherData = async (param) => {
	try {
		const weatherData = await axios.get(
			`${BASE_URL}${param}?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}&units=metric`
		)
		await new Promise((res) => setTimeout(res, 500))
		return weatherData.data
	} catch (err) {
		console.error('ERROR::', err)
	}
}

const weatherData = await getWeatherData('weather')
const forecastData = await getWeatherData('forecast')

forecastData.list.forEach((item) => {
	const timestamp = item.dt * 1000
	item.date = new Date(timestamp).toISOString().split('T')[0]
})

const dailyDataMap = new Map()
forecastData.list.forEach((item) => {
	const date = item.date
	if (!dailyDataMap.has(date)) {
		dailyDataMap.set(date, item)
	}
})

const dailyData = Array.from(dailyDataMap.values())

const removeCity = () => {
	const cities = JSON.parse(localStorage.getItem('savedCities'))
	const updatedCities = cities.filter((city) => city.id !== route.query.id)
	localStorage.setItem('savedCities', JSON.stringify(updatedCities))
	router.push({
		name: 'home',
	})
}
</script>
