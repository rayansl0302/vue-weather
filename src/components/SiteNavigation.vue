<template>
	<!-- Cabeçalho da aplicação -->
	<header class="sticky top-0 bg-weather-primary shadow-lg mx-auto w-full md:w-5/6 rounded-2xl z-10">
	  <!-- Barra de navegação -->
	  <nav class="container flex flex-row items-center justify-between gap-4 text-white py-6 mx-auto">
		<!-- Link para a página inicial -->
		<RouterLink :to="{ name: 'home' }">
		  <div class="flex items-center gap-3">
			<!-- Logo -->
			<Logo class="w-8" />
			<p class="text-2xl font-bold">Aplicativo de clima</p>
		  </div>
		</RouterLink>
  
		<!-- Ícones para informações e adição de cidade (visíveis apenas na visualização) -->
		<div class="flex gap-3">
		  <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
			@click="toggleModal"></i>
		  <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
			@click="addCity" v-if="route.query.preview"></i>
		</div>
  
		<!-- Modal de informações sobre o aplicativo -->
		<BaseModal :modalActive="modalActive" @close-modal="toggleModal">
		  <div class="text-black">
			<h1 class="text-2xl mb-1">Sobre o App:</h1>
			<p class="mb-4">
			  Com este Aplicativo de clima é possível acompanhar a atualidade
			  e o próximo clima nas cidades de sua escolha.
			</p>
			<h2 class="text-2xl">Como funciona</h2>
			<ol class="list-decimal list-inside mb-4">
			  <li>Pesquise sua cidade digitando o nome no campo de pesquisa.</li>
			  <li>
				Selecione uma cidade nos resultados, isso o levará até ela
				clima atual para sua seleção.
			  </li>
			  <li>
				Salve a cidade clicando no ícone "+" no canto superior direito
				a esquina. Isso salvará a cidade para que você possa vê-la de uma só vez
				mais tarde na página inicial.
			  </li>
			</ol>
  
			<h2 class="text-2xl">Excluir uma cidade salva</h2>
			<p>
			  Se você não quiser mais seguir uma cidade, basta selecionar a cidade no
			  pagina inicial. na parte inferior da página há uma opção para remover
			  a cidade.
			</p>
		  </div>
		</BaseModal>
	  </nav>
	</header>
  </template>
  
  <script setup>
  import { uid } from 'uid'
  import { ref } from 'vue'
  import { RouterLink, useRoute, useRouter } from 'vue-router'
  import BaseModal from './BaseModal.vue'
  import Logo from './Logo.vue'
  
  // Hooks do Vue Router
  const route = useRoute()
  const router = useRouter()
  
  // Estado do modal
  const modalActive = ref(null)
  
  // Função para alternar o estado do modal
  const toggleModal = () => {
	modalActive.value = !modalActive.value
  }
  
  // Estado para cidades salvas
  const savedCities = ref([])
  
  // Função para adicionar uma cidade às cidades salvas
  const addCity = () => {
	// Recupera as cidades salvas do armazenamento local, se existirem
	if (localStorage.getItem('savedCities')) {
	  savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
	}
  
	// Cria um objeto de localização com informações da rota
	const locationObject = {
	  id: uid(),
	  state: route.params.state,
	  city: route.params.city,
	  coords: {
		lat: route.query.lat,
		lng: route.query.lng,
	  },
	}
  
	// Adiciona a nova localização à lista de cidades salvas
	savedCities.value.push(locationObject)
  
	// Armazena a lista atualizada no armazenamento local
	localStorage.setItem('savedCities', JSON.stringify(savedCities.value))
  
	// Atualiza a rota removendo o parâmetro de visualização e adicionando o ID da nova cidade
	let query = Object.assign({}, route.query)
	delete query.preview
	query.id = locationObject.id
	router.replace({ query })
  }
  </script>
  