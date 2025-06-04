<template>
  <main>
    <div class="bg-gradient"></div>

    <div class="container">
      <div class="header">
        <h1>AI Vision</h1>
        <!--        <p>Распознавание изображений с помощью искусственного интеллекта</p>-->
      </div>

      <div class="main-grid">
        <!-- Карточка загрузки и анализа -->
        <div class="card">
          <h2 style="margin-bottom: 1.5rem; font-size: 1.5rem;">📸 Анализ изображения</h2>

          <div v-if="!imageUrl"
               class="upload-zone"
               :class="{ dragging: isDragging }"
               @click="selectFile"
               @drop.prevent="handleDrop"
               @dragover.prevent="isDragging = true"
               @dragleave.prevent="isDragging = false">
            <input type="file"
                   ref="fileInput"
                   @change="handleFileSelect"
                   accept="image/*">
            <div class="upload-icon">📤</div>
            <p style="font-size: 1.125rem; margin-bottom: 0.5rem;">Перетащите изображение сюда</p>
            <p style="color: #9ca3af;">или кликните для выбора файла</p>
          </div>

          <div v-else>
            <div class="image-preview">
              <img :src="imageUrl" alt="Загруженное изображение">
            </div>

            <div style="display: flex; gap: 1rem; margin-bottom: 1.5rem;">
              <button class="btn" @click="analyzeImage" :disabled="isLoading">
                <span v-if="!isLoading">🔍 Анализировать</span>
                <span v-else style="display: flex; align-items: center; gap: 0.5rem;">
                                    <span class="loader"></span>
                                    Анализ...
                                </span>
              </button>
              <button class="btn btn-secondary" @click="clearImage">
                🗑️ Очистить
              </button>
            </div>
          </div>

          <div v-if="predictions.length > 0" class="results">
            <h3 style="margin-bottom: 1rem;">Результаты распознавания:</h3>
            <div v-for="(pred, index) in predictions" :key="index" class="result-item">
              <span class="result-label">{{ pred.label }}</span>
              <span class="result-confidence">{{ (pred.confidence * 100).toFixed(1) }}%</span>
            </div>
          </div>

          <div v-if="error" class="error-message">
            ⚠️ {{ error }}
          </div>
        </div>

        <!-- Карточка GPU информации -->
        <div class="card">
          <h2 style="margin-bottom: 1.5rem; font-size: 1.5rem;">🎮 Информация о GPU</h2>

          <div class="gpu-info">
            <div class="gpu-item">
              <span class="gpu-label">Рендерер</span>
              <span class="gpu-value">{{ gpuInfo.renderer || 'Загрузка...' }}</span>
            </div>
            <div class="gpu-item">
              <span class="gpu-label">Производитель</span>
              <span class="gpu-value">{{ gpuInfo.vendor || 'Загрузка...' }}</span>
            </div>
            <div class="gpu-item">
              <span class="gpu-label">WebGL версия</span>
              <span class="gpu-value">{{ gpuInfo.webglVersion || 'Загрузка...' }}</span>
            </div>
            <div class="gpu-item">
              <span class="gpu-label">Максимальный размер текстуры</span>
              <span class="gpu-value">{{ gpuInfo.maxTextureSize || 'Загрузка...' }}</span>
            </div>
            <div class="gpu-item">
              <span class="gpu-label">Максимальные viewport размеры</span>
              <span class="gpu-value">{{ gpuInfo.maxViewportDims || 'Загрузка...' }}</span>
            </div>
            <div class="gpu-item">
              <span class="gpu-label">Поддержка шейдеров</span>
              <span class="gpu-value">{{ gpuInfo.shadingLanguageVersion || 'Загрузка...' }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import {ref, reactive} from 'vue';

// Композиция для работы с изображениями
function useImageUpload() {
  const imageUrl = ref(null);
  const imageFile = ref(null);
  const isDragging = ref(false);
  const fileInput = ref(null);

  const selectFile = () => {
    fileInput.value?.click();
  };

  const handleFileSelect = (e) => {
    const file = e.target.files[0];
    if (file) {
      loadImage(file);
    }
  };

  const handleDrop = (e) => {
    isDragging.value = false;
    const file = e.dataTransfer.files[0];
    if (file && file.type.startsWith('image/')) {
      loadImage(file);
    }
  };

  const loadImage = (file) => {
    imageFile.value = file;
    const reader = new FileReader();
    reader.onload = (e) => {
      imageUrl.value = e.target.result;
    };
    reader.readAsDataURL(file);
  };

  const clearImage = () => {
    imageUrl.value = null;
    imageFile.value = null;
    if (fileInput.value) {
      fileInput.value.value = '';
    }
  };

  return {
    imageUrl,
    imageFile,
    isDragging,
    fileInput,
    selectFile,
    handleFileSelect,
    handleDrop,
    clearImage
  };
}

// Композиция для анализа изображений
function useImageAnalysis() {
  const predictions = ref([]);
  const isLoading = ref(false);
  const error = ref(null);

  const simulateAPICall = async () => {
    await new Promise(resolve => setTimeout(resolve, 2000));

    const possibleLabels = [
      'Кошка', 'Собака', 'Автомобиль', 'Здание', 'Дерево',
      'Человек', 'Цветок', 'Компьютер', 'Телефон', 'Еда'
    ];

    const numPredictions = Math.floor(Math.random() * 3) + 3;
    const results = [];

    for (let i = 0; i < numPredictions; i++) {
      const randomIndex = Math.floor(Math.random() * possibleLabels.length);
      results.push({
        label: possibleLabels[randomIndex],
        confidence: Math.random() * 0.5 + 0.5
      });
      possibleLabels.splice(randomIndex, 1);
    }

    return results.sort((a, b) => b.confidence - a.confidence);
  };

  const analyzeImage = async (imageFile) => {
    if (!imageFile) return;

    isLoading.value = true;
    error.value = null;
    predictions.value = [];

    try {
      // Симуляция API вызова
      predictions.value = await simulateAPICall();

      // В реальном приложении:
      // const formData = new FormData();
      // formData.append('image', imageFile);
      // const response = await fetch('https://your-api.com/predict', {
      //     method: 'POST',
      //     body: formData
      // });
      // const data = await response.json();
      // predictions.value = data.predictions;

    } catch (err) {
      error.value = 'Ошибка при анализе изображения. Попробуйте еще раз.';
      console.error(err);
    } finally {
      isLoading.value = false;
    }
  };

  return {
    predictions,
    isLoading,
    error,
    analyzeImage
  };
}

// Композиция для GPU информации
function useGPUInfo() {
  const gpuInfo = reactive({
    renderer: null,
    vendor: null,
    webglVersion: null,
    maxTextureSize: null,
    maxViewportDims: null,
    shadingLanguageVersion: null
  });

  const getGPUInfo = () => {
    const canvas = document.createElement('canvas');
    const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');

    if (gl) {
      const debugInfo = gl.getExtension('WEBGL_debug_renderer_info');

      gpuInfo.renderer = debugInfo ? gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL) : 'Недоступно';
      gpuInfo.vendor = debugInfo ? gl.getParameter(debugInfo.UNMASKED_VENDOR_WEBGL) : 'Недоступно';
      gpuInfo.webglVersion = gl.getParameter(gl.VERSION);
      gpuInfo.maxTextureSize = gl.getParameter(gl.MAX_TEXTURE_SIZE) + 'px';
      gpuInfo.maxViewportDims = gl.getParameter(gl.MAX_VIEWPORT_DIMS).join(' x ') + 'px';
      gpuInfo.shadingLanguageVersion = gl.getParameter(gl.SHADING_LANGUAGE_VERSION);
    } else {
      Object.keys(gpuInfo).forEach(key => {
        gpuInfo[key] = 'WebGL не поддерживается';
      });
    }
  };

  return {
    gpuInfo,
    getGPUInfo
  };
}

</script>
