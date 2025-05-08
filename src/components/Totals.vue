<template>
  <div>
    <div class="toplamlar">
      <div class="toplam-satir"><span>Ara Toplam</span><span>₺{{ araToplam }}</span></div>
      <div class="toplam-satir"><span>KDV</span><span>₺{{ kdv }}</span></div>
      <div class="toplam-satir"><span>İndirim</span><span>₺{{ indirim }}</span></div>
      <div class="toplam-satir genel-toplam"><span>Genel Toplam</span><span>₺{{ genelToplam }}</span></div>
    </div>
    <div class="butonlar">
      <button class="btn nakit" @click="$emit('nakit')">NAKIT</button>
      <button class="btn kredi" @click="$emit('kredi')">KREDI KARTI</button>
      <button class="btn iade" @click="$emit('iade')">IADE</button>
    </div>
  </div>
</template>
<script setup>
defineProps(['araToplam', 'kdv', 'indirim', 'genelToplam'])
defineEmits(['nakit', 'kredi', 'iade'])
</script>
<style scoped>
.toplamlar {
  margin-top: 8px;
  display: flex;
  flex-direction: column;
  gap: 8px;
  font-size: 1.25rem;
  max-width: 350px;
  align-self: flex-end;
}
.toplam-satir {
  display: flex;
  justify-content: space-between;
}
.genel-toplam {
  font-weight: bold;
  font-size: 1.35rem;
  margin-top: 8px;
  color: #1976d2;
}
.butonlar {
  display: flex;
  gap: 24px;
  margin-top: 24px;
}
.btn {
  flex: 1;
  padding: 22px 0;
  font-size: 1.25rem;
  border: none;
  border-radius: 8px;
  background: #ececec;
  cursor: pointer;
  transition: background 0.2s, box-shadow 0.2s;
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.nakit { background: #ffe082; }
.kredi { background: #b3e5fc; }
.iade { background: #ffcdd2; }
.btn:hover {
  filter: brightness(0.96);
  box-shadow: 0 4px 16px rgba(0,0,0,0.08);
}
</style> 