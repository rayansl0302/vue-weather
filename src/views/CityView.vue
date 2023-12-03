<template>
	<!-- Componente principal para visualização assíncrona da cidade -->
	<div>
	  <!-- Utilização de Suspense para aguardar a conclusão da busca assíncrona -->
	  <Suspense>
		<!-- Componente AsyncCityView exibido quando a busca está completa -->
		<AsyncCityView />
		<!-- Componente CityViewSkeleton exibido durante o carregamento -->
		<template #fallback>
		  <CityViewSkeleton />
		</template>
	  </Suspense>
	</div>
  </template>
  
  <script setup>
  import AsyncCityView from '../components/AsyncCityView.vue'
  import CityViewSkeleton from '../components/CityViewSkeleton.vue'
  </script>
  