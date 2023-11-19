<template>
	<main class="container text-white mx-auto">
		<div class="pt-4 mb-8 relative z-10">
			<input
				type="text"
				v-model="searchQurey"
				@input="getSearchResults"
				placeholder="Sök efter en stad!"
				class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71] text-center"
			/>
			<ul
				v-if="mapBoxSearchResults"
				class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
			>
				<li v-if="searchError">
					Ursäkta, något gick fel. Vänligen försök igen.
				</li>
				<li v-if="!searchError && mapBoxSearchResults.length === 0">
					Inga städer matchar din sökning, försök igen med en riktigt stad....
				</li>
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
		<div class="flex flex-col gap-4">
			<Suspense>
				<CityList />
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

const previewCity = (searchResults) => {
	const [city, state] = searchResults.place_name.split(',')
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

const mapBoxAPIKey = import.meta.env.VITE_MAPBOX_KEY

const searchQurey = ref('')
const queryTimeout = ref(null)
const mapBoxSearchResults = ref(null)
const searchError = ref(null)

const getSearchResults = () => {
	clearTimeout(queryTimeout.value)
	queryTimeout.value = setTimeout(async () => {
		if (searchQurey.value !== '') {
			try {
				searchError.value = false
				const res = await axios.get(
					`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQurey.value}.json?access_token=${mapBoxAPIKey}&types=place`
				)
				mapBoxSearchResults.value = res.data.features
			} catch {
				searchError.value = true
			}
			return
		}
		mapBoxSearchResults.value = null
	}, 300)
}
</script>
