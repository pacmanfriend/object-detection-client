<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-900 to-gray-800 text-white flex flex-col items-center p-6 md:p-12">
    <header class="text-center mb-10">
      <h1 class="text-4xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-blue-400 to-purple-500">
        AI Image Predictor
      </h1>
      <p class="mt-2 text-gray-400">Загрузи изображение и узнай, что на нём!</p>
    </header>

    <main class="w-full max-w-4xl grid grid-cols-1 md:grid-cols-2 gap-8">
      <ImageUploader @image-selected="handleImageUpload" />
      <PredictionResult :result="predictionResult" :isLoading="isPredicting" />
    </main>

    <footer class="mt-12 w-full max-w-4xl">
      <GPUInfo />
    </footer>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import ImageUploader from '@/views/ImageUploader.vue'
import PredictionResult from '@/views/PredictionResult.vue'
import GPUInfo from '@/views/GPUInfo.vue'

const predictionResult = ref(null)
const isPredicting = ref(false)

const handleImageUpload = async (file) => {
  predictionResult.value = null
  isPredicting.value = true

  const formData = new FormData()
  formData.append('image', file)

  try {
    // Предположим, у нас есть API /predict на локальном сервере
    const response = await fetch('http://localhost:5000/predict', {
      method: 'POST',
      body: formData,
    })

    const result = await response.json()
    predictionResult.value = result
  } catch (error) {
    alert('Ошибка при отправке изображения')
    console.error(error)
  } finally {
    isPredicting.value = false
  }
}
</script>