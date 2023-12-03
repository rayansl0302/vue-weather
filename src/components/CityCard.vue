<template>
	<!-- Cartão de cidade exibindo informações básicas com efeito de hover -->
	<div
	  class="flex justify-between py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer hover:scale-105 duration-150 object-contain"
	  :style="{
		backgroundImage: `url('${city.image.web}')`, // Define a imagem de fundo do cartão
		backgroundSize: 'cover',
		backgroundPosition: 'center',
	  }"
	>
	  <!-- Seção esquerda com informações sobre a cidade e estado -->
	  <div class="flex flex-col justify-center bg-weather-secondary opacity-70 px-2 rounded-lg w-fit">
		<h2 class="text-3xl">{{ city.city }}</h2>
		<h3>{{ city.state }}</h3>
	  </div>
	  <!-- Seção direita com informações sobre o clima da cidade -->
	  <div class="flex flex-col gap-2 bg-weather-secondary opacity-70 px-2 rounded-lg">
		<p class="text-3xl self-end">
		  {{ Math.round(city.weather.main.temp) }}&deg;c <!-- Temperatura atual -->
		</p>
		<div class="flex gap-2 self-end">
		  <!-- Temperatura mínima e máxima -->
		  <span class="text-xs">
			Min {{ Math.round(city.weather.main.temp_min) }}&deg;c
		  </span>
		  <span class="text-xs">
			Max {{ Math.round(city.weather.main.temp_max) }}&deg;c
		  </span>
		</div>
		<div class="self-end">
		  <span class="text-xs" v-if="city.weather.rainAmount">
			<!-- Quantidade de chuva, se disponível -->
			<i class="fa-solid fa-cloud-rain"></i>
			{{ city.weather.rainAmount }} m.m&nbsp;
		  </span>
		  <span class="text-xs" v-if="city.weather.snowAmount">
			<!-- Quantidade de neve, se disponível -->
			<i class="fa-solid fa-snowflake"></i>
			{{ city.weather.snowAmount }} m.m&nbsp;
		  </span>
		</div>
	  </div>
	</div>
  </template>
  
  <script setup>
  import { defineProps } from 'vue'
  
  // Define a propriedade "city" como objeto com um valor padrão vazio
  defineProps({
	city: {
	  type: Object,
	  default: () => {},
	},
  })
  </script>
  