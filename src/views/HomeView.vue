<template>
	<!-- Contêiner principal -->
	<main class="container text-white mx-auto">
	  <!-- Entrada de Pesquisa -->
	  <div class="pt-4 mb-8 relative z-10">
		<!-- Input para a consulta de cidades -->
		<input
		  type="text"
		  v-model="searchQurey"
		  @input="getSearchResults"
		  placeholder="Procure uma Cidade, Estado, País ou Região!"
		  class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71] text-center"
		/>
		<!-- Resultados da pesquisa exibidos em uma lista suspensa -->
		<ul
		  v-if="mapBoxSearchResults"
		  class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
		>
		  <!-- Mensagem de erro em caso de falha na pesquisa -->
		  <li v-if="searchError">
			Desculpe, algo deu errado. Por favor, tente novamente.
		  </li>
		  <!-- Mensagem quando não há correspondências encontradas -->
		  <li v-if="!searchError && mapBoxSearchResults.length === 0">
			Nenhuma cidade corresponde à sua pesquisa, tente novamente com uma cidade real....
		  </li>
		  <!-- Exibição dos resultados da pesquisa -->
		  <template v-else>
			<li
			  v-for="searchResults in mapBoxSearchResults"
			  :key="searchResults.id"
			  class="py-2 cursor-pointer"
			  @click="previewCity(searchResults)"
			>
			  {{ searchResults.place_name }}
			</li>
		  </template>
		</ul>
	  </div>
  
	  <!-- Lista de Cidades -->
	  <div class="flex flex-col gap-4">
		<!-- Utilização de Suspense para aguardar a conclusão da busca -->
		<Suspense>
		  <!-- Componente CityList exibido quando a busca está completa -->
		  <CityList />
		  <!-- Componente CityCardSkeleton exibido durante o carregamento -->
		  <template #fallback>
			<CityCardSkeleton />
		  </template>
		</Suspense>
	  </div>
	</main>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import axios from 'axios'
  import { useRouter } from 'vue-router'
  import CityList from '../components/CityList.vue'
  import CityCardSkeleton from '../components/CityCardSkeleton.vue'
  const router = useRouter()
  
  // Função para visualizar uma cidade na pré-visualização
  const previewCity = (searchResults) => {
	const [city, state] = searchResults.place_name.split(',')
	// Navegação para a visualização da cidade selecionada
	router.push({
	  name: 'cityView',
	  params: { state: state.trim(), city: city.trim() },
	  query: {
		lat: searchResults.geometry.coordinates[1],
		lng: searchResults.geometry.coordinates[0],
		preview: true,
	  },
	})
  }
  
  // Chave de API do Mapbox
  const mapBoxAPIKey = 'pk.eyJ1IjoicmF5YW4wMjEwIiwiYSI6ImNscG8ydzBlczBlMzAycW10azRpcWFja3YifQ.DfUOS2ow4cKy6gTmGnkQeg'
  
  // Referência para a consulta de pesquisa
  const searchQurey = ref('')
  const queryTimeout = ref(null)
  const mapBoxSearchResults = ref(null)
  const searchError = ref(null)
  
  // Função para obter resultados de pesquisa do Mapbox
  const getSearchResults = () => {
	clearTimeout(queryTimeout.value)
	// Aguardar um curto período após a última entrada antes de iniciar a pesquisa
	queryTimeout.value = setTimeout(async () => {
	  if (searchQurey.value !== '') {
		try {
		  searchError.value = false
		  // Realizar chamada à API do Mapbox para obter resultados de pesquisa
		  const res = await axios.get(
			`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQurey.value}.json?access_token=${mapBoxAPIKey}&types=place`
		  )
		  // Armazenar os resultados da pesquisa
		  mapBoxSearchResults.value = res.data.features
		} catch {
		  // Sinalizar um erro em caso de falha na chamada à API
		  searchError.value = true
		}
		console.log(mapBoxSearchResults.value)
		return
	  }
	  // Limpar os resultados da pesquisa se a consulta estiver vazia
	  mapBoxSearchResults.value = null
	}, 300)
  }
  </script>
  