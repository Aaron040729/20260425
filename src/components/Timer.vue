<template>
  <div class="tool-section">
    <h2>⏰ 小組報告計時器</h2>
    <div class="timer-display" :class="{ 'warning': timeLeft < 10 && timeLeft > 0 }">
      {{ formatTime(timeLeft) }}
    </div>
    <div class="presets">
      <button @click="setTime(180)">3分鐘</button>
      <button @click="setTime(300)">5分鐘</button>
      <button @click="setTime(600)">10分鐘</button>
    </div>
    <div class="main-controls">
      <button @click="start" v-if="!isRunning">開始</button>
      <button @click="pause" v-else>暫停</button>
      <button @click="reset">重設</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from 'vue';
const timeLeft = ref(300);
const isRunning = ref(false);
let timer = null;

const formatTime = (s) => `${Math.floor(s/60)}:${(s%60).toString().padStart(2, '0')}`;
const setTime = (s) => { timeLeft.value = s; pause(); };
const start = () => {
  if (isRunning.value) return;
  isRunning.value = true;
  timer = setInterval(() => {
    if (timeLeft.value > 0) timeLeft.value--;
    else { pause(); alert('時間到！'); }
  }, 1000);
};
const pause = () => { isRunning.value = false; clearInterval(timer); };
const reset = () => { pause(); timeLeft.value = 300; };
onUnmounted(() => clearInterval(timer));
</script>

<style scoped>
.timer-display { font-size: 5rem; text-align: center; font-family: monospace; padding: 40px; }
.warning { color: #e74c3c; animation: blink 1s infinite; }
@keyframes blink { 0% { opacity: 1; } 50% { opacity: 0.5; } 100% { opacity: 1; } }
.presets, .main-controls { display: flex; justify-content: center; gap: 10px; margin-top: 20px; }
button { padding: 10px 20px; font-size: 1rem; border-radius: 6px; cursor: pointer; border: 1px solid #ddd; }
</style>