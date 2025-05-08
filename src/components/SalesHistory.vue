<template>
  <div class="satis-gecmisi">
    <h3>Satış Geçmişi</h3>
    <table v-if="gecmis.length > 0" class="gecmis-tablosu">
      <thead>
        <tr>
          <th>#</th>
          <th>Tarih</th>
          <th>Ürünler</th>
          <th>Tutar</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(satis, i) in gecmis" :key="satis.id">
          <td>{{ i + 1 }}</td>
          <td>{{ satis.tarih }}</td>
          <td>
            <ul>
              <li v-for="urun in satis.urunler" :key="urun.barkod">
                {{ urun.ad }} x{{ urun.adet }}
              </li>
            </ul>
          </td>
          <td>₺{{ satis.tutar.toFixed(2) }}</td>
        </tr>
      </tbody>
    </table>
    <div v-else style="color:#bbb">Henüz satış yapılmadı</div>
  </div>
</template>
<script setup>
const props = defineProps({
  gecmis: {
    type: Array,
    default: () => []
  }
})
</script>
<style scoped>
.satis-gecmisi {
  margin-top: 32px;
  background: #fff;
  border-radius: 12px;
  padding: 18px 18px 8px 18px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.satis-gecmisi h3 {
  margin-bottom: 12px;
  color: #1976d2;
  font-size: 1.15rem;
}
.gecmis-tablosu {
  width: 100%;
  border-collapse: collapse;
  font-size: 1.05rem;
}
.gecmis-tablosu th, .gecmis-tablosu td {
  border: 1px solid #e0e0e0;
  padding: 8px 6px;
  text-align: center;
}
.gecmis-tablosu th {
  background: #f1f3f6;
  font-weight: 600;
}
</style> 