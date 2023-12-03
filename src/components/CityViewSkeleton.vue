<template>
	<!-- Componente principal para a exibição do esqueleto da visualização da cidade -->
	<div class="flex flex-col flex-1">
	  <!-- Seção de Visão Geral -->
	  <div class="flex flex-col py-12 items-center">
		<!-- Componentes AnimatedPlaceholder para diferentes elementos na visão geral -->
		<AnimatedPlaceholder class="max-w-[300px] h-9 w-full mb-2" />
		<AnimatedPlaceholder class="max-w-[200px] w-full mb-2" />
		<AnimatedPlaceholder class="max-w-[175px] h-[100px] w-full mb-10" />
		<AnimatedPlaceholder class="max-w-[150px] w-full h-[100px] mb-8" />
		<AnimatedPlaceholder class="max-w-[100px] h-4 w-full mb-1" />
		<AnimatedPlaceholder class="max-w-[80px] h-4 w-full mb-1" />
		<AnimatedPlaceholder class="max-w-[80px] h-4 w-full mb-1" />
		<AnimatedPlaceholder class="max-w-[80px] h-4 w-full mb-1" />
		<AnimatedPlaceholder class="max-w-[60px] h-4 w-full mb-1" />
		<AnimatedPlaceholder class="max-w-[60px] h-4 w-full mb-1" />
	  </div>
	  <!-- Seção Horária -->
	  <div class="flex flex-col py-12 px-8 items-center">
		<!-- Componente AnimatedPlaceholder para elementos horários -->
		<AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full mb-12" />
	  </div>
	</div>
  </template>
  
  <script setup>
  import AnimatedPlaceholder from './AnimatedPlaceholder.vue'
  </script>
  