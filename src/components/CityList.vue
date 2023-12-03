<template>
	<!-- Itera sobre as cidades salvas exibindo os cartões de cidade e uma mensagem se não houver cidades salvas -->
	<div>
	  <div v-for="city in savedCities" :key="city.id">
		<!-- Componente CityCard utilizado para exibir informações da cidade -->
		<CityCard :city="city" @click="goToCotyView(city)" />
	  </div>
	  <!-- Mensagem exibida quando não há cidades salvas -->
	  <p v-if="savedCities.length === 0" class="text-center">
		<span class="block">Nenhuma cidade adicionada.</span> Para adicionar um local,
		procure no campo acima.
	  </p>
	</div>
  </template>
  
  <script setup>
  import axios from 'axios'
  import { ref } from 'vue'
  import { useRouter } from 'vue-router'
  import CityCard from './CityCard.vue'
  
  const router = useRouter()
  
  // Referência reativa para armazenar as cidades salvas
  const savedCities = ref([])
  
  // Chave da API OpenWeatherMap
  const API_KEY = 'a8b20fcbf8278d0d407dea0dd7b4763b'
  
  // Função para obter informações das cidades salvas
  const getCities = async () => {
	if (localStorage.getItem('savedCities')) {
	  savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
  
	  // Array de requisições para obter informações do clima e imagem de cada cidade
	  const req = []
  
	  // Aguarda todas as requisições serem concluídas
	  await Promise.all(
		savedCities.value.map(async (city) => {
		  // Requisição para obter informações do clima da cidade
		  const weatherResponse = await axios.get(
			`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${API_KEY}&lang=pt_br&units=metric`
		  )
  
		  // Adiciona as informações do clima à propriedade "weather" do objeto da cidade
		  city.weather = weatherResponse.data
  
		  // Converte o nome da cidade para formato adequado para buscar imagens
		  const cityName = city.weather.name.replace(/\s+/g, '-').toLowerCase()
  
		  try {
			// Requisição para obter imagens da cidade usando a API Teleport
			const teleportResponse = await axios.get(
			  `https://api.teleport.org/api/urban_areas/slug:${cityName}/images/`
			)
  
			// Adiciona a primeira imagem da resposta à propriedade "image" do objeto da cidade
			city.image =
			  teleportResponse.data.photos.length > 0
				? teleportResponse.data.photos[0].image
				: 'URL_TO_FALLBACK_IMAGE'
		  } catch (error) {
			console.info(`Can't find an image for ${cityName}`)
			city.image = '' // Define uma imagem padrão caso não seja encontrada
		  }
		})
	  )
  
	  // Processa as informações do clima para as cidades salvas
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
  
  // Chama a função para obter informações das cidades quando o componente é montado
  await getCities()
  
  // Função para navegar para a visualização detalhada de uma cidade
  const goToCotyView = (city) => {
	router.push({
	  name: 'cityView',
	  params: { state: city.state, city: city.city },
	  query: { id: city.id, lat: city.coords.lat, lng: city.coords.lng },
	})
  }
  </script>
  