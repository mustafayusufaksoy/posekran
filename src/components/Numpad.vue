<template>
  <div class="numaratik">
    <div class="num-row">
      <button @click="$emit('num', 7)">7</button>
      <button @click="$emit('num', 8)">8</button>
      <button @click="$emit('num', 9)">9</button>
    </div>
    <div class="num-row">
      <button @click="$emit('num', 4)">4</button>
      <button @click="$emit('num', 5)">5</button>
      <button @click="$emit('num', 6)">6</button>
    </div>
    <div class="num-row">
      <button @click="$emit('num', 1)">1</button>
      <button @click="$emit('num', 2)">2</button>
      <button @click="$emit('num', 3)">3</button>
    </div>
    <div class="num-row">
      <button @click="$emit('clear')">C</button>
      <button @click="$emit('num', 0)">0</button>
      <button @click="$emit('plus')">+</button>
      <button @click="$emit('minus')">-</button>
    </div>
  </div>
</template>
<script setup>
defineEmits(['num', 'plus', 'minus', 'clear'])
</script>
<style scoped>
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
</style> 