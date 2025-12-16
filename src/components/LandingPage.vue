<script setup lang="ts">
import { Droplets, Search, BarChart3, Shield } from 'lucide-vue-next'
import Button from './ui/Button.vue'

const emit = defineEmits<{
  startSearch: []
}>()

const openHubeauLink = () => {
  window.open('https://hubeau.eaufrance.fr/page/api-qualite-eau-potable', '_blank')
}
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 via-cyan-50 to-teal-50">
    <!-- Hero Section -->
    <div class="relative overflow-hidden">
      <div class="absolute inset-0 bg-grid-slate-100 [mask-image:linear-gradient(0deg,white,rgba(255,255,255,0.6))] -z-10"></div>

      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-20 pb-16 text-center">
        <div class="flex justify-center mb-8">
          <div class="bg-blue-100 p-6 rounded-full shadow-lg">
            <Droplets class="w-20 h-20 text-blue-600" />
          </div>
        </div>

        <h1 class="text-5xl md:text-7xl font-extrabold text-gray-900 mb-6">
          Qualité de l'Eau
          <span class="block text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-cyan-600">
            en France
          </span>
        </h1>

        <p class="text-xl md:text-2xl text-gray-600 mb-8 max-w-3xl mx-auto leading-relaxed">
          Consultez les résultats des contrôles sanitaires de votre commune en temps réel.
          Données officielles de l'API Hub'Eau.
        </p>

        <div class="flex flex-col sm:flex-row gap-4 justify-center items-center">
          <Button
            @click="emit('startSearch')"
            size="lg"
            class="text-lg px-8 py-6 bg-blue-600 hover:bg-blue-700 text-white shadow-xl hover:shadow-2xl transform hover:scale-105 transition-all"
          >
            <Search class="w-5 h-5 mr-2" />
            Rechercher ma commune
          </Button>

          <Button
            variant="outline"
            size="lg"
            class="text-lg px-8 py-6"
            @click="openHubeauLink"
          >
            En savoir plus
          </Button>
        </div>
      </div>
    </div>

    <!-- Features Section -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
      <h2 class="text-3xl md:text-4xl font-bold text-center text-gray-900 mb-12">
        Pourquoi utiliser cette application?
      </h2>

      <div class="grid md:grid-cols-3 gap-8">
        <!-- Feature 1 -->
        <div class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow">
          <div class="bg-blue-100 w-14 h-14 rounded-lg flex items-center justify-center mb-4">
            <Search class="w-7 h-7 text-blue-600" />
          </div>
          <h3 class="text-xl font-bold text-gray-900 mb-3">Recherche Facile</h3>
          <p class="text-gray-600 leading-relaxed">
            Trouvez instantanément les données de qualité d'eau de votre commune par simple recherche.
          </p>
        </div>

        <!-- Feature 2 -->
        <div class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow">
          <div class="bg-green-100 w-14 h-14 rounded-lg flex items-center justify-center mb-4">
            <BarChart3 class="w-7 h-7 text-green-600" />
          </div>
          <h3 class="text-xl font-bold text-gray-900 mb-3">Visualisation Claire</h3>
          <p class="text-gray-600 leading-relaxed">
            Graphiques interactifs pour suivre l'évolution des paramètres de qualité dans le temps.
          </p>
        </div>

        <!-- Feature 3 -->
        <div class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow">
          <div class="bg-purple-100 w-14 h-14 rounded-lg flex items-center justify-center mb-4">
            <Shield class="w-7 h-7 text-purple-600" />
          </div>
          <h3 class="text-xl font-bold text-gray-900 mb-3">Données Officielles</h3>
          <p class="text-gray-600 leading-relaxed">
            Informations provenant directement de l'API Hub'Eau du gouvernement français.
          </p>
        </div>
      </div>
    </div>

    <!-- Stats Section -->
    <div class="bg-gradient-to-r from-blue-600 to-cyan-600 py-16">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="grid md:grid-cols-3 gap-8 text-center text-white">
          <div>
            <div class="text-5xl font-bold mb-2">+35 000</div>
            <div class="text-xl opacity-90">Communes</div>
          </div>
          <div>
            <div class="text-5xl font-bold mb-2">100%</div>
            <div class="text-xl opacity-90">Gratuit</div>
          </div>
          <div>
            <div class="text-5xl font-bold mb-2">24/7</div>
            <div class="text-xl opacity-90">Accessible</div>
          </div>
        </div>
      </div>
    </div>

    <!-- CTA Section -->
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 py-20 text-center">
      <h2 class="text-3xl md:text-4xl font-bold text-gray-900 mb-6">
        Prêt à vérifier la qualité de votre eau?
      </h2>
      <p class="text-xl text-gray-600 mb-8">
        Accédez aux données de nitrates, dureté, bactériologie et plus encore.
      </p>
      <Button
        @click="emit('startSearch')"
        size="lg"
        class="text-xl px-10 py-7 bg-blue-600 hover:bg-blue-700 text-white shadow-xl hover:shadow-2xl transform hover:scale-105 transition-all"
      >
        <Droplets class="w-6 h-6 mr-2" />
        Commencer maintenant
      </Button>
    </div>

    <!-- Footer -->
    <footer class="bg-gray-900 text-white py-8">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
        <p class="text-gray-400">
          Données fournies par l'API Hub'Eau - Ministère de la Transition écologique
        </p>
        <p class="text-gray-500 mt-2 text-sm">
          Application créée pour le défi Dev de Yohan Dev
        </p>
      </div>
    </footer>
  </div>
</template>

<style scoped>
.bg-grid-slate-100 {
  background-size: 40px 40px;
  background-image:
    linear-gradient(to right, rgba(148, 163, 184, 0.1) 1px, transparent 1px),
    linear-gradient(to bottom, rgba(148, 163, 184, 0.1) 1px, transparent 1px);
}
</style>
