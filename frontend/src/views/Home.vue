<template>
  <div class="work-container">
    <!-- Header -->
    <header class="work-header">
      <div class="brand">СИМУЛЯТОР</div>
      <div class="status-text">MiroFish Engine v1.0</div>
    </header>

    <div class="work-layout">
      <!-- Left: Instructions -->
      <aside class="instructions-panel">
        <h2>Инструкция</h2>
        <div class="steps-list">
          <div class="step-item">
            <span class="step-num">1</span>
            <div>
              <strong>Загрузите документ</strong>
              <p>PDF, MD или TXT — описание продукта, рынка, проекта или любой контекст для симуляции.</p>
            </div>
          </div>
          <div class="step-item">
            <span class="step-num">2</span>
            <div>
              <strong>Опишите задачу</strong>
              <p>Что нужно смоделировать? Какой вопрос исследовать? На каком языке отчёт?</p>
            </div>
          </div>
          <div class="step-item">
            <span class="step-num">3</span>
            <div>
              <strong>Запустите движок</strong>
              <p>Система построит граф знаний, создаст агентов и запустит симуляцию (20-40 раундов).</p>
            </div>
          </div>
          <div class="step-item">
            <span class="step-num">4</span>
            <div>
              <strong>Получите отчёт</strong>
              <p>Аналитический отчёт + возможность опросить любого агента симуляции.</p>
            </div>
          </div>
        </div>

        <div class="info-block">
          
          <strong>Время:</strong> 30-60 минут<br>
          <strong>Агенты:</strong> 10-20 персон рынка (генерируются автоматически)
        </div>
      </aside>

      <!-- Right: Work Area -->
      <main class="work-panel">
        <!-- File Upload -->
        <div class="upload-section">
          <h3>Документ</h3>
          <div 
            class="drop-zone"
            :class="{ 'drag-over': isDragOver }"
            @dragover.prevent="isDragOver = true"
            @dragleave="isDragOver = false"
            @drop.prevent="handleDrop"
            @click="triggerFile"
          >
            <input 
              ref="fileInput" 
              type="file" 
              multiple 
              accept=".pdf,.md,.txt" 
              style="display:none" 
              @change="handleFileSelect"
            />
            <div v-if="files.length === 0" class="drop-placeholder">
              Перетащите файл сюда или нажмите для выбора<br>
              <small>PDF, MD, TXT (до 50 МБ)</small>
            </div>
            <div v-else class="file-list">
              <div v-for="(file, i) in files" :key="i" class="file-item">
                <span>{{ file.name }}</span>
                <button @click.stop="files.splice(i, 1)" class="remove-btn">&times;</button>
              </div>
            </div>
          </div>
        </div>

        <!-- Simulation Prompt -->
        <div class="prompt-section">
          <h3>Задача симуляции</h3>
          <textarea
            v-model="requirement"
            class="prompt-input"
            placeholder="Опишите что нужно смоделировать. Например: Как отреагируют брокеры и инвесторы на наш новый продукт? Какие модули каталога наиболее востребованы? Отчёт на русском."
            rows="5"
            :disabled="loading"
          ></textarea>
        </div>

        <!-- Error -->
        <div v-if="error" class="error-msg">{{ error }}</div>

        <!-- Launch Button -->
        <button 
          class="launch-btn"
          @click="launch"
          :disabled="!canSubmit || loading"
        >
          <span v-if="!loading">Запустить симуляцию →</span>
          <span v-else>Инициализация...</span>
        </button>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue"
import { useRouter } from "vue-router"

const router = useRouter()
const requirement = ref("")
const files = ref([])
const loading = ref(false)
const error = ref("")
const isDragOver = ref(false)
const fileInput = ref(null)

const canSubmit = computed(() => requirement.value.trim() && files.value.length > 0)

const handleFileSelect = (e) => {
  const selected = Array.from(e.target.files).filter(f => {
    const ext = f.name.split(".").pop().toLowerCase()
    return ["pdf", "md", "txt"].includes(ext)
  })
  files.value.push(...selected)
}

const handleDrop = (e) => {
  isDragOver.value = false
  if (loading.value) return
  const dropped = Array.from(e.dataTransfer.files).filter(f => {
    const ext = f.name.split(".").pop().toLowerCase()
    return ["pdf", "md", "txt"].includes(ext)
  })
  files.value.push(...dropped)
}

const triggerFile = () => { if (fileInput.value) fileInput.value.click() }

const launch = () => {
  if (!canSubmit.value || loading.value) return
  import("../store/pendingUpload.js").then(({ setPendingUpload }) => {
    setPendingUpload(files.value, requirement.value)
    router.push({ name: "Process", params: { projectId: "new" } })
  })
}
</script>

<style scoped>
.work-container {
  min-height: 100vh;
  background: #fafafa;
  font-family: "Inter", system-ui, sans-serif;
  color: #1a1a1a;
}

.work-header {
  height: 50px;
  background: #111;
  color: #fff;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 24px;
}

.brand {
  font-weight: 700;
  font-size: 14px;
  letter-spacing: 2px;
}

.status-text {
  font-size: 12px;
  color: #888;
  font-family: "JetBrains Mono", monospace;
}

.work-layout {
  display: flex;
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px 24px;
  gap: 32px;
}

/* Instructions Panel */
.instructions-panel {
  width: 340px;
  flex-shrink: 0;
}

.instructions-panel h2 {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 20px;
  color: #333;
}

.steps-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-bottom: 24px;
}

.step-item {
  display: flex;
  gap: 12px;
  align-items: flex-start;
}

.step-num {
  width: 28px;
  height: 28px;
  background: #111;
  color: #fff;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 13px;
  font-weight: 600;
  flex-shrink: 0;
  margin-top: 2px;
}

.step-item strong {
  font-size: 14px;
  display: block;
  margin-bottom: 4px;
}

.step-item p {
  font-size: 13px;
  color: #666;
  margin: 0;
  line-height: 1.5;
}

.info-block {
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  padding: 16px;
  font-size: 13px;
  line-height: 1.8;
  color: #555;
}

/* Work Panel */
.work-panel {
  flex: 1;
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 12px;
  padding: 28px;
}

.work-panel h3 {
  font-size: 15px;
  font-weight: 600;
  margin-bottom: 12px;
  color: #333;
}

.upload-section {
  margin-bottom: 24px;
}

.drop-zone {
  border: 2px dashed #ddd;
  border-radius: 8px;
  padding: 32px;
  text-align: center;
  cursor: pointer;
  transition: all 0.2s;
}

.drop-zone:hover, .drop-zone.drag-over {
  border-color: #FF4500;
  background: #fff8f5;
}

.drop-placeholder {
  color: #999;
  font-size: 14px;
}

.drop-placeholder small {
  color: #bbb;
  font-size: 12px;
}

.file-list {
  text-align: left;
}

.file-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  background: #f7f7f7;
  border-radius: 6px;
  margin-bottom: 6px;
  font-size: 13px;
}

.remove-btn {
  background: none;
  border: none;
  color: #999;
  font-size: 18px;
  cursor: pointer;
  padding: 0 4px;
}

.remove-btn:hover {
  color: #ff4500;
}

.prompt-section {
  margin-bottom: 24px;
}

.prompt-input {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 14px;
  font-size: 14px;
  font-family: inherit;
  resize: vertical;
  line-height: 1.5;
  box-sizing: border-box;
}

.prompt-input:focus {
  outline: none;
  border-color: #FF4500;
}

.error-msg {
  background: #fff0f0;
  border: 1px solid #ffcccc;
  color: #cc0000;
  padding: 10px 14px;
  border-radius: 6px;
  font-size: 13px;
  margin-bottom: 16px;
}

.launch-btn {
  width: 100%;
  padding: 14px;
  background: #111;
  color: #fff;
  border: none;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.launch-btn:hover:not(:disabled) {
  background: #FF4500;
}

.launch-btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}
</style>
