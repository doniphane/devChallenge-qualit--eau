<script setup lang="ts">
import { ref, nextTick } from 'vue'
import Button from './ui/Button.vue'

declare const Chart: any

interface CommuneData {
  annee: string
  code_commune: string
  code_reseau: string
  debut_alim: string
  nom_commune: string
  nom_quartier: string
  nom_reseau: string
}

interface AnalyseResult {
  code_commune: string
  nom_commune: string
  code_reseau: string
  nom_reseau: string
  date_prelevement: string
  code_parametre: string
  libelle_parametre: string
  resultat_alphanumerique: string
  unite_mesure: string
  conclusion_conformite_prelevement: string
}

interface ApiResponse {
  count: number
  data: CommuneData[]
}

interface AnalyseApiResponse {
  count: number
  data: AnalyseResult[]
}

const searchQuery = ref('')
const results = ref<CommuneData[]>([])
const analyseResults = ref<AnalyseResult[]>([])
const loading = ref(false)
const error = ref('')
const selectedCommune = ref<CommuneData | null>(null)
const showAnalyses = ref(false)
const currentPage = ref(1)
const itemsPerPage = ref(50)
let chart: any = null

const searchCommune = async () => {
  if (!searchQuery.value.trim()) {
    error.value = 'Veuillez entrer un nom de commune'
    return
  }

  loading.value = true
  error.value = ''
  results.value = []
  showAnalyses.value = false
  analyseResults.value = []

  try {
    const response = await fetch(
      `https://hubeau.eaufrance.fr/api/v1/qualite_eau_potable/communes_udi?nom_commune=${encodeURIComponent(searchQuery.value)}&size=100`
    )

    if (!response.ok) {
      throw new Error('Erreur lors de la recherche')
    }

    const data: ApiResponse = await response.json()
    results.value = data.data

    if (data.data.length === 0) {
      error.value = 'Aucun résultat trouvé pour cette commune'
    }
  } catch (err) {
    error.value = 'Erreur lors de la recherche. Veuillez réessayer.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const viewAnalyses = async (commune: CommuneData) => {
  selectedCommune.value = commune
  loading.value = true
  error.value = ''
  currentPage.value = 1

  try {
    const response = await fetch(
      `https://hubeau.eaufrance.fr/api/v1/qualite_eau_potable/resultats_dis?code_commune=${commune.code_commune}&size=500&sort=desc`
    )

    if (!response.ok) {
      throw new Error('Erreur lors de la récupération des analyses')
    }

    const data: AnalyseApiResponse = await response.json()
    analyseResults.value = data.data
    showAnalyses.value = true

    await nextTick()
    createChart()
  } catch (err) {
    error.value = 'Erreur lors de la récupération des analyses.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const paginatedResults = () => {
  const start = (currentPage.value - 1) * itemsPerPage.value
  const end = start + itemsPerPage.value
  return analyseResults.value.slice(start, end)
}

const totalPages = () => {
  return Math.ceil(analyseResults.value.length / itemsPerPage.value)
}

const goToPage = (page: number) => {
  currentPage.value = page
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const nextPage = () => {
  if (currentPage.value < totalPages()) {
    currentPage.value++
    window.scrollTo({ top: 0, behavior: 'smooth' })
  }
}

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
    window.scrollTo({ top: 0, behavior: 'smooth' })
  }
}

const getPageNumbers = () => {
  const total = totalPages()
  const current = currentPage.value
  const pages: (number | string)[] = []

  if (total <= 7) {
    for (let i = 1; i <= total; i++) {
      pages.push(i)
    }
  } else {
    pages.push(1)

    if (current > 3) {
      pages.push('...')
    }

    for (let i = Math.max(2, current - 1); i <= Math.min(total - 1, current + 1); i++) {
      pages.push(i)
    }

    if (current < total - 2) {
      pages.push('...')
    }

    pages.push(total)
  }

  return pages
}

const createChart = () => {
  if (chart) {
    chart.destroy()
  }

  const canvas = document.getElementById('analysesChart') as HTMLCanvasElement
  if (!canvas) {
    console.log('Canvas non trouvé')
    return
  }

  const parameterGroups: Record<string, AnalyseResult[]> = {}
  analyseResults.value.forEach(result => {
    if (result.resultat_alphanumerique && !isNaN(parseFloat(result.resultat_alphanumerique))) {
      if (!parameterGroups[result.libelle_parametre]) {
        parameterGroups[result.libelle_parametre] = []
      }
      parameterGroups[result.libelle_parametre].push(result)
    }
  })

  if (Object.keys(parameterGroups).length === 0) {
    console.log('Aucune donnée numérique à afficher')
    return
  }

  const topParameters = Object.entries(parameterGroups)
    .sort((a, b) => b[1].length - a[1].length)
    .slice(0, 5)

  const datasets = topParameters.map((entry, index) => {
    const [paramName, results] = entry
    const colors = ['#3b82f6', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6']

    const sortedResults = results
      .sort((a, b) => new Date(a.date_prelevement).getTime() - new Date(b.date_prelevement).getTime())
      .slice(-20)

    return {
      label: paramName,
      data: sortedResults.map(r => ({
        x: r.date_prelevement,
        y: parseFloat(r.resultat_alphanumerique)
      })),
      borderColor: colors[index],
      backgroundColor: colors[index] + '33',
      borderWidth: 2,
      pointRadius: 4,
      pointHoverRadius: 6,
      tension: 0.3,
      fill: false
    }
  })

  try {
    chart = new Chart(canvas, {
      type: 'line',
      data: { datasets },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        interaction: {
          mode: 'index',
          intersect: false,
        },
        scales: {
          x: {
            type: 'time',
            time: {
              unit: 'day',
              displayFormats: {
                day: 'dd/MM/yyyy'
              }
            },
            title: {
              display: true,
              text: 'Date de prélèvement',
              font: {
                size: 14,
                weight: 'bold'
              }
            },
            ticks: {
              maxRotation: 45,
              minRotation: 45
            }
          },
          y: {
            beginAtZero: false,
            title: {
              display: true,
              text: 'Valeur mesurée',
              font: {
                size: 14,
                weight: 'bold'
              }
            }
          }
        },
        plugins: {
          legend: {
            display: true,
            position: 'top',
            labels: {
              font: {
                size: 12
              },
              padding: 15,
              usePointStyle: true
            }
          },
          tooltip: {
            backgroundColor: 'rgba(0,0,0,0.8)',
            padding: 12,
            callbacks: {
              label: (context: any) => {
                const label = context.dataset.label || ''
                const value = context.parsed.y
                return `${label}: ${value.toFixed(2)}`
              }
            }
          }
        }
      }
    })
  } catch (error) {
    console.error('Erreur lors de la création du graphique:', error)
  }
}

const backToResults = () => {
  showAnalyses.value = false
  selectedCommune.value = null
  currentPage.value = 1
  if (chart) {
    chart.destroy()
    chart = null
  }
}
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-cyan-50">
    <div class="max-w-7xl mx-auto px-4 py-8">
      <!-- Search Section -->
      <div v-if="!showAnalyses" class="mb-8">
        <div class="bg-white rounded-lg shadow-lg p-6">
          <div class="flex gap-3">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="🔍 Entrez le nom d'une commune (ex: Paris, Lyon, Marseille...)"
              @keyup.enter="searchCommune"
              class="flex-1 px-4 py-3 border-2 border-gray-300 rounded-lg focus:border-blue-500 focus:outline-none text-lg"
            />
            <Button
              @click="searchCommune"
              :disabled="loading"
              class="px-8 py-3"
            >
              {{ loading ? 'Recherche...' : 'Rechercher' }}
            </Button>
          </div>

          <!-- Quick Examples -->
          <div class="mt-4 pt-4 border-t border-gray-200">
            <p class="text-sm text-gray-600 mb-2">Essayez ces exemples:</p>
            <div class="flex flex-wrap gap-2">
              <Button
                v-for="example in ['La Rochelle', 'Orléans', 'Paris', 'Lyon', 'Marseille']"
                :key="example"
                @click="searchQuery = example; searchCommune()"
                variant="outline"
                size="sm"
              >
                {{ example }}
              </Button>
            </div>
          </div>
        </div>

        <!-- Error Message -->
        <div v-if="error" class="mt-4 bg-red-100 border-l-4 border-red-500 text-red-700 p-4 rounded">
          <p class="font-bold">Erreur</p>
          <p>{{ error }}</p>
        </div>

        <!-- Results List -->
        <div v-if="results.length > 0" class="mt-8">
          <h2 class="text-2xl font-bold text-gray-800 mb-4">
            📊 Résultats trouvés: {{ results.length }}
          </h2>
          <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
            <div
              v-for="(result, index) in results"
              :key="index"
              class="bg-white rounded-lg shadow-md hover:shadow-xl transition-shadow p-6 cursor-pointer border-l-4 border-blue-500"
              @click="viewAnalyses(result)"
            >
              <h3 class="text-xl font-bold text-blue-700 mb-3">
                {{ result.nom_commune }}
              </h3>
              <div class="space-y-2 text-sm">
                <p class="text-gray-700">
                  <span class="font-semibold">Réseau:</span> {{ result.nom_reseau }}
                </p>
                <p class="text-gray-600">
                  <span class="font-semibold">Code réseau:</span> {{ result.code_reseau }}
                </p>
                <p class="text-gray-600">
                  <span class="font-semibold">Année:</span> {{ result.annee }}
                </p>
                <p v-if="result.nom_quartier" class="text-gray-600">
                  <span class="font-semibold">Quartier:</span> {{ result.nom_quartier }}
                </p>
              </div>
              <div class="mt-4 text-blue-600 font-semibold text-sm">
                Cliquez pour voir les analyses →
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Analyses Detail View -->
      <div v-if="showAnalyses && selectedCommune" class="space-y-6">
        <Button
          @click="backToResults"
          variant="outline"
        >
          ← Retour aux résultats
        </Button>

        <div class="bg-white rounded-lg shadow-lg p-6">
          <h2 class="text-3xl font-bold text-blue-900 mb-2">
            {{ selectedCommune.nom_commune }}
          </h2>
          <p class="text-gray-600 mb-4">
            Réseau: {{ selectedCommune.nom_reseau }}
          </p>

          <!-- Info Cards -->
          <div v-if="analyseResults.length > 0" class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
            <div class="bg-gradient-to-br from-blue-50 to-blue-100 rounded-lg p-4 border border-blue-200">
              <div class="text-2xl mb-2">💧</div>
              <div class="text-sm font-semibold text-gray-700">Nitrates (NO3-)</div>
              <div class="text-xs text-gray-600 mt-1">Limite: 50 mg/L</div>
            </div>
            <div class="bg-gradient-to-br from-green-50 to-green-100 rounded-lg p-4 border border-green-200">
              <div class="text-2xl mb-2">🔬</div>
              <div class="text-sm font-semibold text-gray-700">Dureté (TH)</div>
              <div class="text-xs text-gray-600 mt-1">Pour régler votre lave-vaisselle</div>
            </div>
            <div class="bg-gradient-to-br from-purple-50 to-purple-100 rounded-lg p-4 border border-purple-200">
              <div class="text-2xl mb-2">🦠</div>
              <div class="text-sm font-semibold text-gray-700">Bactériologie</div>
              <div class="text-xs text-gray-600 mt-1">Conformité sanitaire</div>
            </div>
          </div>

          <!-- Chart -->
          <div v-if="analyseResults.length > 0" class="mt-6">
            <h3 class="text-xl font-bold text-gray-800 mb-4">
              📈 Évolution des paramètres de qualité (Top 5)
            </h3>
            <div class="bg-gray-50 rounded-lg p-4" style="height: 450px;">
              <canvas id="analysesChart"></canvas>
            </div>
          </div>

          <!-- Results Table -->
          <div v-if="analyseResults.length > 0" class="mt-8">
            <h3 class="text-xl font-bold text-gray-800 mb-4">
              🧪 Résultats des analyses ({{ analyseResults.length }} résultats)
            </h3>
            <div class="overflow-x-auto">
              <table class="min-w-full bg-white border border-gray-300">
                <thead class="bg-blue-100">
                  <tr>
                    <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Date</th>
                    <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Paramètre</th>
                    <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Résultat</th>
                    <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Unité</th>
                    <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Conformité</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="(analyse, idx) in paginatedResults()"
                    :key="idx"
                    class="hover:bg-gray-50 border-b"
                  >
                    <td class="px-4 py-3 text-sm text-gray-700">
                      {{ new Date(analyse.date_prelevement).toLocaleDateString('fr-FR') }}
                    </td>
                    <td class="px-4 py-3 text-sm text-gray-800 font-medium">
                      {{ analyse.libelle_parametre }}
                    </td>
                    <td class="px-4 py-3 text-sm text-gray-700">
                      {{ analyse.resultat_alphanumerique || 'N/A' }}
                    </td>
                    <td class="px-4 py-3 text-sm text-gray-600">
                      {{ analyse.unite_mesure || '-' }}
                    </td>
                    <td class="px-4 py-3 text-sm">
                      <span
                        :class="{
                          'bg-green-100 text-green-800': analyse.conclusion_conformite_prelevement === 'C',
                          'bg-red-100 text-red-800': analyse.conclusion_conformite_prelevement === 'N',
                          'bg-gray-100 text-gray-800': !analyse.conclusion_conformite_prelevement
                        }"
                        class="px-2 py-1 rounded text-xs font-semibold"
                      >
                        {{ analyse.conclusion_conformite_prelevement === 'C' ? '✓ Conforme' :
                           analyse.conclusion_conformite_prelevement === 'N' ? '✗ Non conforme' :
                           'N/A' }}
                      </span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>

            <!-- Pagination -->
            <div v-if="analyseResults.length > itemsPerPage" class="mt-6 flex flex-col items-center gap-4">
              <p class="text-sm text-gray-600">
                Affichage de {{ (currentPage - 1) * itemsPerPage + 1 }} à {{ Math.min(currentPage * itemsPerPage, analyseResults.length) }} sur {{ analyseResults.length }} résultats
              </p>

              <div class="flex items-center gap-2">
                <!-- Bouton Précédent -->
                <Button
                  @click="prevPage"
                  :disabled="currentPage === 1"
                  variant="outline"
                  size="sm"
                  class="px-3"
                >
                  ← Précédent
                </Button>

                <!-- Numéros de page -->
                <div class="flex gap-1">
                  <template v-for="(page, index) in getPageNumbers()" :key="index">
                    <Button
                      v-if="page !== '...'"
                      @click="goToPage(page as number)"
                      :variant="currentPage === page ? 'default' : 'outline'"
                      size="sm"
                      class="w-10 h-10"
                    >
                      {{ page }}
                    </Button>
                    <span v-else class="flex items-center px-2 text-gray-500">...</span>
                  </template>
                </div>

                <!-- Bouton Suivant -->
                <Button
                  @click="nextPage"
                  :disabled="currentPage === totalPages()"
                  variant="outline"
                  size="sm"
                  class="px-3"
                >
                  Suivant →
                </Button>
              </div>
            </div>
          </div>

          <div v-if="analyseResults.length === 0 && !loading" class="text-center py-8">
            <p class="text-gray-600">Aucune analyse disponible pour cette commune.</p>
          </div>

          <div v-if="loading" class="text-center py-8">
            <p class="text-gray-600">Chargement des analyses...</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
