<template>
	<div
		class="flex justify-between py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer hover:scale-105 duration-150 object-contain"
		:style="{
			backgroundImage: `url('${city.image.web}')`,
			backgroundSize: 'cover',
			backgroundPosition: 'center',
		}"
	>
		<div
			class="flex flex-col justify-center bg-weather-secondary opacity-70 px-2 rounded-lg w-fit"
		>
			<h2 class="text-3xl">{{ city.city }}</h2>
			<h3>{{ city.state }}</h3>
		</div>
		<div
			class="flex flex-col gap-2 bg-weather-secondary opacity-70 px-2 rounded-lg"
		>
			<p class="text-3xl self-end">
				{{ Math.round(city.weather.main.temp) }}&deg;c
			</p>
			<div class="flex gap-2 self-end">
				<span class="text-xs">
					L
					{{ Math.round(city.weather.main.temp_min) }}&deg;c
				</span>
				<span class="text-xs">
					H
					{{ Math.round(city.weather.main.temp_max) }}&deg;c
				</span>
			</div>
			<div class="self-end">
				<span class="text-xs" v-if="city.weather.rainAmount">
					<i class="fa-solid fa-cloud-rain"></i>
					{{ city.weather.rainAmount }} m.m&nbsp;
				</span>

				<span class="text-xs" v-if="city.weather.snowAmount">
					<i class="fa-solid fa-snowflake"></i>
					{{ city.weather.snowAmount }} m.m&nbsp;</span
				>
			</div>
		</div>
	</div>
</template>

<script setup>
defineProps({
	city: {
		type: Object,
		default: () => {},
	},
})
</script>
