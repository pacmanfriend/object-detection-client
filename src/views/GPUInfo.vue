<template>
  <div class="bg-gray-800 rounded-xl shadow-md p-4 text-center text-sm">
    <h2 class="font-semibold mb-2 flex items-center justify-center gap-2">
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 inline text-blue-400">
        <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 9.75h16.5m-16.5 3h16.5" />
      </svg>
      Информация о GPU
    </h2>
    <p v-if="gpuInfo" class="text-gray-300">{{ gpuInfo }}</p>
    <p v-else class="text-gray-500">Невозможно получить данные.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const gpuInfo = ref(null)

onMounted(() => {
  const canvas = document.createElement('canvas')
  const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl')

  if (gl && typeof WebGLRenderingContext !== 'undefined') {
    const debugInfo = gl.getExtension('WEBGL_debug_renderer_info')
    if (debugInfo) {
      const renderer = gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL)
      gpuInfo.value = renderer
    } else {
      gpuInfo.value = 'Данные о GPU недоступны.'
    }
  } else {
    gpuInfo.value = 'WebGL не поддерживается.'
  }
})
</script>