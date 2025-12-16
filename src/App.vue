<script setup lang="ts">
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import LandingPage from './components/LandingPage.vue'
import SearchPage from './components/SearchPage.vue'

const currentPage = ref<'landing' | 'search'>('landing')

const goToSearch = () => {
  currentPage.value = 'search'
}

const goHome = () => {
  currentPage.value = 'landing'
}
</script>

<template>
  <div>
    <Navbar :show-back-button="currentPage === 'search'" @go-home="goHome" />
    <LandingPage v-if="currentPage === 'landing'" @start-search="goToSearch" />
    <SearchPage v-if="currentPage === 'search'" />
  </div>
</template>
