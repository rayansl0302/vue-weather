<template>
	<div class="flex flex-col flex-1 items-center">
	  <!-- Banner -->
	  <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
		<p>Clique no ícone "+" para adicionar esta cidade à sua lista.</p>
	  </div>
  
	  <!-- Visão geral do clima -->
	  <div class="flex flex-col items-center text-white py-4">
		<!-- Nome da cidade e data/hora atual -->
		<h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
		<p class="text-sm">
		  {{
			new Date().toLocaleDateString('pt-BR', {
			  weekday: 'long',
			  day: '2-digit',
			  month: 'short',
			})
		  }}
		  {{
			new Date(weatherData.dt * 1000).toLocaleTimeString('pt-BR', {
			  timeStyle: 'short',
			})
		  }}
		</p>
  
		<!-- Ícone atual de temperatura e clima -->
		<p class="text-8xl">{{ ((Math.round(weatherData.main.temp) - 32) * 5 / 9).toFixed(1) }}&deg;c</p>
		<img class="w-[100px] h-auto" :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
		  :alt="`${weatherData.weather[0].description}`" :title="`${weatherData.weather[0].description}`" />
  
		<!-- Detalhes do clima atual -->
		<p class="capitalize">Condição climática: <strong>{{ weatherData.weather[0].description }} </strong></p>
		<p class="text-xl">
		  Sensação térmica: {{ (Math.round(weatherData.main.feels_like - 32) * 5 / 9).toFixed(1) }}&deg;c
		</p>
		<p>
		  Min {{ ((Math.round(weatherData.main.temp_min) - 32) * 5 / 9).toFixed(1) }}&deg;c / Max
		  {{ ((Math.round(weatherData.main.temp_max) - 32) * 5 / 9).toFixed(1) }}&deg;c
		</p>
		<p>
		  <i class="fa-solid fa-wind"></i> {{ weatherData.wind.speed }} Km/s
		  <i :style="{ transform: `rotate(${weatherData.wind.deg}deg)` }" class="fa-solid fa-angles-down"></i>
		</p>
		<p v-if="weatherData.rainAmount">
		  <i class="fa-solid fa-cloud-rain"></i>
		  {{ weatherData.rainAmount }}<span> m.m</span>
		</p>
		<p v-if="weatherData.snowAmount">
		  <i class="fa-solid fa-snowflake"></i>
		  {{ weatherData.snowAmount }}<span> m.m</span>
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
  
	  <!-- Tempo de hora em hora -->
	  <div class="max-w-screen-md w-full py-4 mx-auto text-center">
		<div class="mx-8 text-white">
		  <h2 class="mb-4 text-lg font-bold">Previsão do dia seguinte:</h2>
		  <div v-if="forecastData && forecastData.list" class="flex gap-10 overflow-x-scroll md:justify-center py-4">
			<div v-for="hourData in forecastData.list.slice(0, 9)" :key="hourData.dt"
			  class="flex flex-col gap-1 items-center text-center">
			  <p class="whitespace-nowrap text-md capitalize">
				{{
				  new Date(hourData.dt_txt).toLocaleTimeString('pt-BR', {
					weekday: 'long',
					hour: 'numeric',
					minute: 'numeric',
				  })
				}}
			  </p>
			  <img class="w-auto h-[50px] object-cover"
				:src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" alt="" />
			  <p class="capitalize text-sm">{{ hourData.weather[0].description }}</p>
			  <p class="text-xl">{{ ((Math.round(hourData.main.temp) - 32) * 5 / 9).toFixed(1) }}&deg;c</p>
			  <p class="text-xs">
				<span class="block">Min &nbsp; {{ ((Math.round(hourData.main.temp_min) - 32) * 5 / 9).toFixed(1) }}&deg;c
				</span>
				<span class="block">Max &nbsp; {{ ((Math.round(hourData.main.temp_max) - 32) * 5 / 9).toFixed(1) }}&deg;c
				</span>
			  </p>
			  <p class="text-xs" v-if="hourData.rainAmount">
				<i v-if="hourData.rainAmount" class="fa-solid fa-cloud-rain"></i>
				&nbsp; {{ hourData.rainAmount }} m.m
			  </p>
			  <p class="text-xs" v-if="hourData.snowAmount">
				<i class="fa-solid fa-snowflake"></i>
				{{ hourData.snowAmount }}<span> m.m</span>
			  </p>
			</div>
		  </div>
		</div>
	  </div>
  
	  <hr class="border-white border-opacity-10 border w-full mb-4" />
  
	  <!-- Clima Semanal -->
	  <div class="max-w-screen-md w-full py-4 text-center">
		<div class="mx-8 text-white">
		  <h2 class="mb-4 text-lg font-bold">Previsão da semana:</h2>
		  <div class="flex gap-10 overflow-x-scroll md:justify-center py-4">
			<div v-for="daily in dailyData" :key="daily.dt" class="flex flex-col gap-1 items-center">
			  <p class="whitespace-nowrap text-md capitalize">
				{{
				  new Date(daily.dt_txt.replace(/-/g, '/')).toLocaleDateString(
					'pt-BR',
					{
					  weekday: 'long',
					}
				  )
				}}
			  </p>
			  <img class="w-auto h-[50px] object-cover"
				:src="`http://openweathermap.org/img/wn/${daily.weather[0].icon}@2x.png`" alt="" />
			  <p class="capitalize text-sm">{{ daily.weather[0].description }}</p>
			  <p class="text-xl">{{ ((Math.round(daily.main.temp) - 32) * 5 / 9).toFixed(1) }}&deg;c</p>
			  <p class="text-xs">
				<span class="block">Min &nbsp; {{ ((Math.round(daily.main.temp_min) - 32) * 5 / 9).toFixed(1) }}&deg;c
				</span>
				<span class="block">Max &nbsp; {{ ((Math.round(daily.main.temp_max) - 32) * 5 / 9).toFixed(1) }}&deg;c
				</span>
			  </p>
			  <p class="text-xs" v-if="daily.rainAmount">
				<i class="fa-solid fa-cloud-rain"></i>
				{{ daily.rainAmount }} m.m
			  </p>
			  <p class="text-xs" v-if="daily.snowAmount">
				<i class="fa-solid fa-snowflake"></i>
				{{ daily.snowAmount }} m.m
			  </p>
			</div>
		  </div>
		</div>
	  </div>
	</div>
  
	<hr class="border-white border-opacity-10 border w-full mb-4" />
  
	<!-- Botão de remover a cidade -->
	<div v-if="!route.query.preview"
	  class="flex items-center justify-center gap-2 py-8 text-white cursor-pointer duration-150 hover:text-red-500"
	  @click="removeCity">
	  <i class="fa-solid fa-trash"></i>
	  <p>Remover cidade</p>
	</div>
  </template>
  
  <script setup>
  import axios from 'axios'
  import { useRoute, useRouter } from 'vue-router'
  
  const route = useRoute()
  const router = useRouter()
  
  const API_KEY = '392084f1c34f3f9e5038a9d00e73cc80'
  const BASE_URL = 'https://api.openweathermap.org/data/2.5/'
  
  // Função para obter dados do clima
  const getWeatherData = async (param) => {
	try {
	  const response = await axios.get(
		`${BASE_URL}${param}?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}&lang=pt_br&units=imperial`
	  )
  
	  await new Promise((res) => setTimeout(res, 500))
  
	  const weatherData = response.data
  
	  // Adiciona dados de chuva e neve se disponíveis
	  if (weatherData && weatherData.rain) {
		weatherData.rainAmount = Object.values(weatherData.rain)[0]
	  }
  
	  if (weatherData && weatherData.snow) {
		weatherData.snowAmount = Object.values(weatherData.snow)[0]
	  }
  
	  return weatherData
	} catch (err) {
	  console.error('Erro na requisição à API:', err);
	}
  }
  
  // Obtém dados do clima atual e da previsão
  const weatherData = await getWeatherData('weather')
  const forecastData = await getWeatherData('forecast')
  console.log("forecastData", forecastData);
  
  // Cria um mapa para armazenar dados diários
  const dailyDataMap = new Map()
  
  if (forecastData && forecastData.list) {
	console.log("Forecast Data List", forecastData.list);
  
	if (forecastData.list.length === 0) {
	  console.error("Forecast Data List is empty");
	} else {
	  // Limpa dailyDataMap antes de adicionar novos itens
	  dailyDataMap.clear();
  
	  forecastData.list.forEach((item) => {
		console.log("Processing forecast item", item);
  
		// Adiciona um log para verificar as propriedades de cada item
		console.log("Item properties", Object.keys(item));
  
		const timestamp = item.dt * 1000;
		item.date = new Date(timestamp).toISOString().split('T')[0];
  
		// Adiciona dados de chuva e neve se disponíveis
		if (item.rain) {
		  item.rainAmount = Object.values(item.rain)[0];
		}
  
		if (item.snow) {
		  item.snowAmount = Object.values(item.snow)[0];
		}
  
		// Adiciona ao dailyDataMap usando a data como chave
		if (item.date) {
		  if (!dailyDataMap.has(item.date)) {
			dailyDataMap.set(item.date, item);
		  }
		} else {
		  console.warn('Item de forecastData não possui uma propriedade "date" definida:', item);
		}
  
		console.log("Processed forecast item", item);
	  });
	}
  } else {
	console.error("Forecast Data List is empty or undefined");
  }
  
  // Converte dailyDataMap.values() para array
  const dailyData = Array.from(dailyDataMap.values());
  console.log("Daily Data", dailyData);
  
  // Função para remover a cidade da lista
  const removeCity = () => {
	const cities = JSON.parse(localStorage.getItem('savedCities')) || []
	const updatedCities = cities.filter((city) => city.id !== route.query.id)
	localStorage.setItem('savedCities', JSON.stringify(updatedCities))
	router.push({
	  name: 'home',
	})
  }
  </script>
  