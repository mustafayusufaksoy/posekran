<template>
  <div>
    <video ref="videoRef" style="width: 100%; max-width: 340px; margin: 0 auto; border-radius: 8px;" autoplay muted></video>
    <button @click="stopScanner" v-if="scanning" class="scanner-stop">Durdur</button>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { BrowserMultiFormatReader } from '@zxing/browser'

const emit = defineEmits(['barkod'])
const videoRef = ref(null)
const scanning = ref(false)
let codeReader
let controls = null

function startScanner() {
  scanning.value = true
  codeReader = new BrowserMultiFormatReader()
  controls = codeReader.decodeFromVideoDevice(
    null,
    videoRef.value,
    (result, err) => {
      if (result) {
        console.log('[BARKOD OKUNDU]', result.getText())
        emit('barkod', result.getText())
        stopScanner()
      }
    }
  )
}

function stopScanner() {
  scanning.value = false
  if (controls && typeof controls.stop === 'function') {
    controls.stop()
    controls = null
  }
}

onMounted(() => {
  startScanner()
})
onBeforeUnmount(() => {
  stopScanner()
})
</script>

<style scoped>
.scanner-stop {
  display: block;
  margin: 18px auto 0 auto;
  padding: 10px 24px;
  background: #ffcdd2;
  color: #b71c1c;
  border: none;
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
}
</style> 