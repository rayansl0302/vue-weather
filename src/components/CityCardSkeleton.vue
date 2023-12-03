<template>
	<!-- Componente para exibir um esqueleto de informações da cidade -->
	<div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md">
	  <!-- Seção da esquerda com informações principais -->
	  <div class="flex flex-col flex-1 gap-2">
		<!-- Componentes AnimatedPlaceholder para diferentes elementos na seção esquerda -->
		<AnimatedPlaceholder class="max-w-[50%]" />
		<AnimatedPlaceholder class="max-w-[40%]" />
	  </div>
	  <!-- Seção da direita com informações adicionais -->
	  <div class="flex flex-col items-end flex-1 gap-2">
		<!-- Componentes AnimatedPlaceholder para diferentes elementos na seção direita -->
		<AnimatedPlaceholder class="max-w-[50px] w-full" />
		<AnimatedPlaceholder class="max-w-[75px] w-full" />
	  </div>
	</div>
  </template>
  
  <script setup>
  import AnimatedPlaceholder from './AnimatedPlaceholder.vue'
  </script>
  