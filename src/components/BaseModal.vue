<template>
	<!-- Componente de modal usando Teleport e Transitions -->
	<Teleport to="body">
	  <Transition name="modal-outer">
		<!-- Camada externa do modal para transições de opacidade -->
		<div
		  v-show="modalActive"
		  class="absolute w-full bg-black bg-opacity-30 h-screen top-0 left-0 flex justify-center"
		>
		  <!-- Transição para a camada interna do modal -->
		  <Transition name="modal-inner">
			<div
			  v-if="modalActive"
			  class="p-4 bg-white self-start mt-32 max-w-screen-md"
			>
			  <!-- Conteúdo do modal fornecido pelo usuário através do slot -->
			  <slot />
			  <!-- Botão de fechar o modal -->
			  <button
				class="text-white mt-8 bg-weather-primary py-2 px-6"
				@click="$emit('close-modal')"
			  >
				Fechar
			  </button>
			</div>
		  </Transition>
		</div>
	  </Transition>
	</Teleport>
  </template>
  
  <script setup>
  // Define a propriedade "modalActive" como booleana com valor padrão false
  defineProps({
	modalActive: {
	  type: Boolean,
	  default: false,
	},
  })
  </script>
  
  <style scoped>
  /* Estilos específicos para as transições de entrada e saída do modal */
  
  /* Transição da camada externa do modal */
  .modal-outer-enter-active,
  .modal-outer-leave-active {
	transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }
  
  .modal-outer-enter-from,
  .modal-outer-leave-to {
	opacity: 0;
  }
  
  /* Transição da camada interna do modal */
  .modal-inner-enter-active {
	transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
  }
  
  .modal-inner-leave-active {
	transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }
  
  .modal-inner-enter-from {
	opacity: 0;
	transform: scale(0.8);
  }
  
  .modal-inner-leave-to {
	transform: scale(0.8);
  }
  </style>
  