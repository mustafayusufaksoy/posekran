<template>
  <div class="sidebar" :class="{ acik: acik }">
    <div class="sidebar-header">
      <span class="sidebar-title">Menü</span>
      <button class="sidebar-close" @click="$emit('close')">×</button>
    </div>
    <ul class="sidebar-list">
      <li>
        <button class="sidebar-btn" @click="$emit('gecmis')">Geçmiş</button>
      </li>
      <!-- Diğer menü butonları buraya eklenebilir -->
    </ul>
  </div>
</template>
<script setup>
const props = defineProps({
  acik: Boolean
})
</script>
<style scoped>
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 260px;
  height: 100vh;
  background: #fff;
  box-shadow: 2px 0 16px rgba(0,0,0,0.08);
  transform: translateX(-100%);
  transition: transform 0.25s;
  z-index: 2000;
  display: flex;
  flex-direction: column;
}
.sidebar.acik {
  transform: translateX(0);
}
.sidebar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 18px 18px 8px 18px;
  border-bottom: 1px solid #eee;
}
.sidebar-title {
  font-weight: bold;
  color: #1976d2;
  font-size: 1.15rem;
}
.sidebar-close {
  background: none;
  border: none;
  font-size: 1.7rem;
  cursor: pointer;
  color: #888;
}
.sidebar-list {
  list-style: none;
  padding: 0;
  margin: 0;
  margin-top: 18px;
}
.sidebar-btn {
  width: 100%;
  padding: 14px 0;
  border: none;
  background: #f4f6fa;
  color: #1976d2;
  font-size: 1.1rem;
  font-weight: 600;
  border-radius: 6px;
  margin-bottom: 10px;
  cursor: pointer;
  transition: background 0.2s;
}
.sidebar-btn:hover {
  background: #e3f2fd;
}
</style> 