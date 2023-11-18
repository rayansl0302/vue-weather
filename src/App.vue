<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary vue-bg">
    <SiteNavigation />
    <RouterView />
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router'
import SiteNavigation from './components/SiteNavigation.vue'
</script>

<style style="scss" scoped></style>
