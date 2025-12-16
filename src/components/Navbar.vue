<script setup lang="ts">
import { Droplets, Home } from 'lucide-vue-next'
import Button from './ui/Button.vue'

interface Props {
  showBackButton?: boolean
}

defineProps<Props>()

const emit = defineEmits<{
  goHome: []
}>()
</script>

<template>
  <nav class="bg-white shadow-md sticky top-0 z-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between items-center h-16">
        <div class="flex items-center space-x-3 cursor-pointer" @click="emit('goHome')">
          <div class="bg-blue-600 p-2 rounded-lg">
            <Droplets class="w-6 h-6 text-white" />
          </div>
          <span class="text-xl font-bold text-gray-900">Qualité Eau France</span>
        </div>

        <div v-if="showBackButton">
          <Button
            @click="emit('goHome')"
            variant="outline"
            class="flex items-center gap-2"
          >
            <Home class="w-4 h-4" />
            Accueil
          </Button>
        </div>
      </div>
    </div>
  </nav>
</template>
