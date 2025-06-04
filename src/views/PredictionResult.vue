<template>
  <div class="bg-gray-800 rounded-xl shadow-lg p-6 flex flex-col items-center transition hover:shadow-purple-500/30">
    <h2 class="text-xl font-semibold mb-4">Результат</h2>

    <div v-if="isLoading" class="flex flex-col items-center">
      <svg class="animate-spin h-10 w-10 text-blue-500 mb-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
      <p class="text-gray-300">Идет анализ...</p>
    </div>

    <div v-else-if="result" class="text-center">
      <p class="text-2xl font-bold text-green-400">Результат:</p>
      <p class="mt-2 text-lg text-gray-200">{{ result.prediction }}</p>
    </div>

    <div v-else class="text-center text-gray-400">
      <p>Пока ничего не загружено.</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  result: {
    type: Object,
    default: null
  },
  isLoading: {
    type: Boolean,
    default: false
  }
})
</script>