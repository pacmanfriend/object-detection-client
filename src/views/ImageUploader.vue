<template>
  <div class="bg-gray-800 rounded-xl shadow-lg p-6 flex flex-col items-center transition hover:shadow-blue-500/30">
    <h2 class="text-xl font-semibold mb-4">Загрузите изображение</h2>

    <label class="w-full flex flex-col items-center justify-center border-2 border-dashed border-gray-600 rounded-lg p-6 cursor-pointer hover:bg-gray-700/30 transition">
      <input type="file" accept="image/*" @change="onFileChange" class="hidden" />
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-10 h-10 text-gray-400 mb-2">
        <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
      </svg>
      <span class="text-sm text-gray-400">Выберите файл или перетащите сюда</span>
    </label>

    <img v-if="previewUrl" :src="previewUrl" alt="Preview" class="max-w-full h-auto rounded mt-4 shadow-md" />
  </div>
</template>

<script setup>
import { ref } from 'vue'

const previewUrl = ref(null)

const emit = defineEmits(['image-selected'])

const onFileChange = (event) => {
  const file = event.target.files[0]
  if (file) {
    previewUrl.value = URL.createObjectURL(file)
    emit('image-selected', file)
  }
}
</script>