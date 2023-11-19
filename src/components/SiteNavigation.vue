<template>
	<header
		class="sticky top-0 bg-weather-primary shadow-lg mx-auto w-full md:w-5/6 rounded-2xl z-10"
	>
		<nav
			class="container flex flex-row items-center justify-between gap-4 text-white py-6 mx-auto"
		>
			<RouterLink :to="{ name: 'home' }">
				<div class="flex items-center gap-3">
					<Logo class="w-8" />
					<p class="text-2xl font-bold">Väder App</p>
				</div>
			</RouterLink>

			<div class="flex gap-3">
				<i
					class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
					@click="toggleModal"
				></i>
				<i
					class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
					@click="addCity"
					v-if="route.query.preview"
				></i>
			</div>

			<BaseModal :modalActive="modalActive" @close-modal="toggleModal">
				<div class="text-black">
					<h1 class="text-2xl mb-1">Om</h1>
					<p class="mb-4">
						Med denna Väder App är det möjligt för dig att följa det aktuella
						och kommande vädret i städer som du själv väljer.
					</p>
					<h2 class="text-2xl">Hur det fungerar</h2>
					<ol class="list-decimal list-inside mb-4">
						<li>Sök efter din stad genom att ange namnet i sökfältet.</li>
						<li>
							Välj en stad bland resultaten, detta kommer ta dig till det
							aktuella vädret för ditt val.
						</li>
						<li>
							Spara staden genom att klicka på "+"-ikonen i det övre högra
							hörnet. Detta kommer att spara staden så att du kan se den vid ett
							senare tillfälle på startsidan.
						</li>
					</ol>

					<h2 class="text-2xl">Ta bort en sparad stad</h2>
					<p>
						Om du inte längre vill följa en stad, välj helt enkelt staden på
						startsidan. längst ner på sidan finns det ett alternativ att ta bort
						staden.
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

const route = useRoute()
const router = useRouter()

const modalActive = ref(null)
const toggleModal = () => {
	modalActive.value = !modalActive.value
}

const savedCities = ref([])
const addCity = () => {
	if (localStorage.getItem('savedCities')) {
		savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
	}

	const locationObject = {
		id: uid(),
		state: route.params.state,
		city: route.params.city,
		coords: {
			lat: route.query.lat,
			lng: route.query.lng,
		},
	}
	savedCities.value.push(locationObject)
	localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

	let query = Object.assign({}, route.query)
	delete query.preview
	query.id = locationObject.id
	router.replace({ query })
}
</script>
