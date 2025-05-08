<template>
  <table class="urun-tablosu">
    <thead>
      <tr>
        <th>Ürün Adı</th>
        <th>Miktar</th>
        <th>Fiyat</th>
        <th>Tutar</th>
        <th>Sil</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(urun, i) in sepettekiler"
        :key="urun.barkod"
        :class="{ secili: seciliIndex === i }"
      >
        <td>{{ urun.ad }}</td>
        <td>
          <input
            class="miktar-input"
            type="number"
            min="1"
            :value="urun.adet"
            readonly
            @click.stop="$emit('secili', i)"
          />
        </td>
        <td>₺{{ urun.fiyat.toFixed(2) }}</td>
        <td>₺{{ (urun.fiyat * urun.adet).toFixed(2) }}</td>
        <td><button class="sil-btn" @click.stop="$emit('sil', i)">x</button></td>
      </tr>
      <tr v-if="sepettekiler.length === 0">
        <td colspan="5" style="color:#bbb">Henüz ürün eklenmedi</td>
      </tr>
    </tbody>
  </table>
</template>
<script setup>
defineProps(['sepettekiler', 'seciliIndex'])
defineEmits(['secili', 'sil'])
</script>
<style scoped>
.urun-tablosu {
  width: 100%;
  border-collapse: collapse;
  font-size: 1.2rem;
  margin-bottom: 12px;
}
.urun-tablosu th, .urun-tablosu td {
  border: 1.5px solid #e0e0e0;
  padding: 14px 8px;
  text-align: center;
}
.urun-tablosu th {
  background: #f1f3f6;
  font-weight: 600;
  font-size: 1.15em;
}
.urun-tablosu tr.secili {
  background: #e3f2fd;
}
.sil-btn {
  background: #ffcdd2;
  border: none;
  border-radius: 4px;
  padding: 4px 10px;
  cursor: pointer;
  font-weight: bold;
  color: #b71c1c;
}
.miktar-input {
  width: 56px;
  padding: 6px 4px;
  font-size: 1.1rem;
  border: 1px solid #b0bec5;
  border-radius: 6px;
  text-align: center;
  background: #f8fafc;
}
.miktar-input:focus {
  outline: 2px solid #1976d2;
  background: #e3f2fd;
}
</style> 