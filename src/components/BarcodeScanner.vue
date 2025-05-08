<template>
  <div>
    <div class="scanner-info">Kameranın netliğine dikkat edin. Barkodu kameraya çok yakın tutmayın.</div>
    <div id="reader" style="width: 320px; margin: 0 auto;"></div>
    <button @click="stopScanner" v-if="scanning" class="scanner-stop">Durdur</button>
  </div>
</template>
<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue'
let Html5Qrcode
const emit = defineEmits(['barkod'])
const scanning = ref(false)
let html5QrCode
const scannerActive = ref(false)

async function startScanner() {
  scanning.value = true
  if (!Html5Qrcode) {
    Html5Qrcode = (await import('html5-qrcode')).Html5Qrcode
  }
  html5QrCode = new Html5Qrcode('reader')
  scannerActive.value = true
  html5QrCode.start(
    { facingMode: 'environment' },
    { fps: 5, qrbox: 320 },
    (decodedText) => {
      console.log('[BARKOD OKUNDU]', decodedText)
      emit('barkod', decodedText)
      stopScanner()
    },
    (errorMessage) => {
      // Her frame'de hata olabilir, çok fazla log olmasın diye sadece ilk 1-2 hatayı gösterelim
      if (errorMessage && errorMessage.length < 100) {
        console.debug('[BARKOD HATA]', errorMessage)
      }
    }
  ).catch(e => {
    console.error('[BARKOD BAŞLATMA HATASI]', e)
    scannerActive.value = false
  })
}

function stopScanner() {
  scanning.value = false
  if (html5QrCode && scannerActive.value) {
    html5QrCode.stop()
      .then(() => html5QrCode.clear())
      .catch(e => {
        console.debug('[BARKOD STOP HATASI]', e)
      })
    scannerActive.value = false
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
.scanner-info {
  text-align: center;
  color: #1976d2;
  font-size: 1.05rem;
  margin-bottom: 8px;
}
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