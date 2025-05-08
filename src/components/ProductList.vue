<template>
  <div class="ornek-urunler">
    <div class="ornek-baslik">Örnek Ürünler</div>
    <div class="ornek-chip-list">
      <button
        v-for="urun in urunler"
        :key="urun.barkod"
        class="ornek-chip"
        @click="$emit('ekle', urun.barkod)"
      >
        <span class="ornek-ad">{{ urun.ad }}</span>
        <span class="ornek-barkod">({{ urun.barkod }})</span>
      </button>
    </div>
    <input
      class="urun-input"
      :value="inputValue"
      @input="$emit('update:inputValue', $event.target.value)"
      @keyup.enter="$emit('ekle', inputValue)"
      placeholder="Ürün adı veya barkod okutun"
    />
  </div>
</template>
<script setup>
defineProps(['urunler', 'inputValue'])
defineEmits(['ekle', 'update:inputValue'])
</script>
<style scoped>
.ornek-urunler {
  margin-bottom: 18px;
  background: #f8fafc;
  border-radius: 8px;
  padding: 12px 16px 8px 16px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.03);
}
.ornek-baslik {
  font-weight: bold;
  margin-bottom: 6px;
  color: #1976d2;
  font-size: 1.1rem;
}
.ornek-chip-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}
.ornek-chip {
  background: #e3f2fd;
  border: none;
  border-radius: 16px;
  padding: 7px 16px;
  cursor: pointer;
  font-size: 1rem;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: background 0.2s, box-shadow 0.2s;
  box-shadow: 0 1px 4px rgba(25, 118, 210, 0.07);
  font-weight: 500;
}
.ornek-chip:hover {
  background: #bbdefb;
  box-shadow: 0 2px 8px rgba(25, 118, 210, 0.13);
}
.ornek-ad {
  font-weight: 500;
}
.ornek-barkod {
  color: #888;
  font-size: 0.95em;
}
.urun-input {
  width: 100%;
  padding: 20px;
  font-size: 1.4rem;
  border-radius: 8px;
  border: 1.5px solid #b0bec5;
  margin-top: 12px;
  background: #f8fafc;
}
</style> 