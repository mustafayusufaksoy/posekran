<script setup>
import { ref } from 'vue'
import ProductList from './components/ProductList.vue'
import CartTable from './components/CartTable.vue'
import Numpad from './components/Numpad.vue'
import Totals from './components/Totals.vue'
import SalesHistory from './components/SalesHistory.vue'
import SidebarMenu from './components/SidebarMenu.vue'
import BarcodeScanner from './components/BarcodeScanner.vue'

// Örnek ürünler
const urunlerListesi = [
  { barkod: '123456', ad: 'Kola 1L', fiyat: 25 },
  { barkod: '234567', ad: 'Cips 150g', fiyat: 18 },
  { barkod: '345678', ad: 'Çikolata', fiyat: 12 },
  { barkod: '456789', ad: 'Su 0.5L', fiyat: 6 },
]

const barkodInput = ref('')
const sepettekiler = ref([])
const seciliIndex = ref(null)
const adetYazmaModu = ref(false)
const satisGecmisi = ref([])
const sidebarAcik = ref(false)
const gecmisAcik = ref(false)
const barkodScannerAcik = ref(false)

function urunEkle(barkodOrAd) {
  const giris = (barkodOrAd ?? barkodInput.value).trim()
  if (!giris) return
  const urun = urunlerListesi.find(u => u.barkod === giris || u.ad.toLowerCase() === giris.toLowerCase())
  if (urun) {
    const mevcut = sepettekiler.value.find(item => item.barkod === urun.barkod)
    if (mevcut) {
      mevcut.adet++
    } else {
      sepettekiler.value.push({ ...urun, adet: 1 })
    }
    barkodInput.value = ''
  }
}

function urunSil(index) {
  sepettekiler.value.splice(index, 1)
  if (seciliIndex.value === index) seciliIndex.value = null
}

function seciliYap(index) {
  seciliIndex.value = index
  adetYazmaModu.value = true
}

function adetGir(num) {
  if (seciliIndex.value === null) return
  const urun = sepettekiler.value[seciliIndex.value]
  if (!urun) return
  if (adetYazmaModu.value) {
    urun.adet = num
    adetYazmaModu.value = false
  } else {
    urun.adet = Number(String(urun.adet || '') + num)
  }
}

function adetArttir() {
  if (seciliIndex.value === null) return
  sepettekiler.value[seciliIndex.value].adet++
  adetYazmaModu.value = false
}

function adetAzalt() {
  if (seciliIndex.value === null) return
  if (sepettekiler.value[seciliIndex.value].adet > 1)
    sepettekiler.value[seciliIndex.value].adet--
  adetYazmaModu.value = false
}

function adetSifirla() {
  if (seciliIndex.value === null) return
  sepettekiler.value[seciliIndex.value].adet = 1
  adetYazmaModu.value = false
}

function adetKontrol(urun) {
  if (urun.adet < 1 || isNaN(urun.adet)) urun.adet = 1
}

const KDV_ORAN = 0.08
const INDIRIM = 0

function araToplam() {
  return sepettekiler.value.reduce((t, u) => t + u.fiyat * u.adet, 0)
}
function kdv() {
  return araToplam() * KDV_ORAN
}
function genelToplam() {
  return araToplam() + kdv() - INDIRIM
}

function satisYap(tip) {
  if (sepettekiler.value.length === 0) return
  const satis = {
    id: Date.now(),
    tarih: new Date().toLocaleString('tr-TR'),
    urunler: sepettekiler.value.map(u => ({ ...u })),
    tutar: genelToplam(),
    tip
  }
  satisGecmisi.value.unshift(satis)
  sepettekiler.value = []
  seciliIndex.value = null
}

function barkodOkundu(barkod) {
  urunEkle(barkod)
  barkodScannerAcik.value = false
}
</script>

<template>
  <div class="pos-bg">
    <!-- Barkod Tara butonu sağ üstte -->
    <button class="barkod-btn" @click="barkodScannerAcik = true">
      <span class="barkod-icon">📷</span> Barkod Tara
    </button>
    <!-- Barkod Okuyucu Modalı -->
    <div v-if="barkodScannerAcik" class="modal-gecmis">
      <div class="modal-icerik">
        <button class="modal-kapat" @click="barkodScannerAcik = false">×</button>
        <BarcodeScanner @barkod="barkodOkundu" />
      </div>
    </div>
    <!-- Sol üstte hamburger menü butonu -->
    <button v-if="!sidebarAcik" class="menu-btn" @click="sidebarAcik = true">
      <span class="menu-icon"></span>
      <span class="menu-icon"></span>
      <span class="menu-icon"></span>
    </button>
    <!-- Sidebar -->
    <SidebarMenu :acik="sidebarAcik" @close="sidebarAcik = false" @gecmis="sidebarAcik = false; gecmisAcik = true" />
    <!-- Geçmiş Modalı -->
    <div v-if="gecmisAcik" class="modal-gecmis">
      <div class="modal-icerik">
        <button class="modal-kapat" @click="gecmisAcik = false">×</button>
        <SalesHistory :gecmis="satisGecmisi" />
      </div>
    </div>
    <div class="pos-iki-kolon">
      <!-- SOL: Ürün ekleme ve tablo -->
      <div class="sol-panel">
        <ProductList
          :urunler="urunlerListesi"
          :inputValue="barkodInput"
          @update:inputValue="barkodInput = $event"
          @ekle="urunEkle"
        />
        <CartTable
          :sepettekiler="sepettekiler"
          :seciliIndex="seciliIndex"
          @secili="seciliYap"
          @sil="urunSil"
        />
      </div>
      <!-- SAĞ: Numaratik, toplamlar, butonlar -->
      <div class="sag-panel">
        <Numpad
          @num="adetGir"
          @plus="adetArttir"
          @minus="adetAzalt"
          @clear="adetSifirla"
        />
        <Totals
          :araToplam="araToplam().toFixed(2)"
          :kdv="kdv().toFixed(2)"
          :indirim="INDIRIM.toFixed(2)"
          :genelToplam="genelToplam().toFixed(2)"
          @nakit="() => satisYap('nakit')"
          @kredi="() => satisYap('kredi')"
          @iade="() => {}"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.pos-bg {
  min-height: 100vh;
  width: 100vw;
  background: #f4f6fa;
  display: flex;
  align-items: stretch;
  justify-content: stretch;
}
.pos-iki-kolon {
  display: flex;
  flex-direction: row;
  width: 100vw;
  max-width: 1600px;
  margin: auto;
  min-height: 100vh;
  gap: 0;
}
.sol-panel {
  flex: 2;
  background: #fff;
  padding: 48px 32px 32px 48px;
  display: flex;
  flex-direction: column;
  border-top-left-radius: 24px;
  border-bottom-left-radius: 24px;
  box-shadow: 0 4px 32px rgba(0,0,0,0.10);
}
.sag-panel {
  flex: 1;
  background: #f8fafc;
  padding: 48px 40px 32px 40px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  border-top-right-radius: 24px;
  border-bottom-right-radius: 24px;
  box-shadow: 0 4px 32px rgba(0,0,0,0.10);
  min-width: 340px;
}
.urun-input {
  width: 100%;
  padding: 20px;
  font-size: 1.4rem;
  border-radius: 8px;
  border: 1.5px solid #b0bec5;
  margin-bottom: 18px;
  background: #f8fafc;
}
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
.numaratik {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 32px;
}
.num-row {
  display: flex;
  gap: 8px;
}
.num-row button {
  flex: 1;
  padding: 18px 0;
  font-size: 1.2rem;
  border: none;
  border-radius: 8px;
  background: #ececec;
  cursor: pointer;
  font-weight: 600;
  transition: background 0.2s;
}
.num-row button:hover {
  background: #b3e5fc;
}
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
@media (max-width: 1000px) {
  .pos-iki-kolon {
    flex-direction: column;
    min-width: 100vw;
  }
  .sol-panel, .sag-panel {
    border-radius: 0;
    min-width: 100vw;
    box-shadow: none;
    padding: 18px 2vw;
  }
  .sag-panel {
    min-width: 100vw;
  }
}
@media (max-width: 600px) {
  .sol-panel, .sag-panel {
    padding: 8px 2vw;
  }
  .toplamlar {
    max-width: 100vw;
    font-size: 1.1rem;
  }
  .btn {
    font-size: 1.1rem;
    padding: 14px 0;
  }
}
.menu-btn {
  position: fixed;
  top: 24px;
  left: 24px;
  z-index: 2100;
  background: #fff;
  border: none;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  padding: 10px 12px;
  display: flex;
  flex-direction: column;
  gap: 4px;
  cursor: pointer;
}
.menu-icon {
  width: 28px;
  height: 4px;
  background: #1976d2;
  border-radius: 2px;
  display: block;
}
.modal-gecmis {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.18);
  z-index: 3000;
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-icerik {
  background: #fff;
  border-radius: 16px;
  padding: 32px 32px 18px 32px;
  min-width: 340px;
  min-height: 320px;
  max-width: 96vw;
  max-height: 90vh;
  overflow: auto;
  position: relative;
  box-shadow: 0 8px 32px rgba(0,0,0,0.13);
}
.modal-kapat {
  position: absolute;
  top: 12px;
  right: 18px;
  background: none;
  border: none;
  font-size: 2.1rem;
  color: #888;
  cursor: pointer;
  z-index: 1;
}
.barkod-btn {
  position: fixed;
  top: 24px;
  right: 24px;
  z-index: 2100;
  background: #e3f2fd;
  color: #1976d2;
  border: none;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(25,118,210,0.08);
  padding: 12px 22px;
  font-size: 1.15rem;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  transition: background 0.2s;
}
.barkod-btn:hover {
  background: #bbdefb;
}
.barkod-icon {
  font-size: 1.3em;
}
</style>
