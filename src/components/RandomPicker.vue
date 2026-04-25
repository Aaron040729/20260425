<template>
  <div class="tool-section">
    <h2>🎯 隨機抽籤機</h2>
    <div class="picker-layout">
      <div class="input-side">
        <label>名單匯入 (每行一個名字):</label>
        <textarea v-model="nameInput" placeholder="輸入學生姓名..."></textarea>
        <div class="options">
          <input type="checkbox" v-model="excludePicked" id="exclude">
          <label for="exclude">抽中後排除</label>
        </div>
      </div>
      
      <div class="display-side">
        <div class="winner-box" :class="{ spinning: isSpinning }">
          {{ currentName || '準備抽籤' }}
        </div>
        <button @click="startSpin" :disabled="isSpinning || !names.length" class="btn-primary">
          {{ isSpinning ? '挑選中...' : '開始抽籤' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const nameInput = ref("王小明\n李華\n張強\n陳曉東\n林美美");
const excludePicked = ref(false);
const isSpinning = ref(false);
const currentName = ref('');

const names = computed(() => nameInput.value.split('\n').filter(n => n.trim() !== ''));

const startSpin = () => {
  if (names.value.length === 0) return;
  isSpinning.value = true;
  let counter = 0;
  const maxTicks = 20;
  
  const interval = setInterval(() => {
    const randomIndex = Math.floor(Math.random() * names.value.length);
    currentName.value = names.value[randomIndex];
    counter++;
    
    if (counter > maxTicks) {
      clearInterval(interval);
      isSpinning.value = false;
      if (excludePicked.value) {
        const updatedList = names.value.filter(n => n !== currentName.value);
        nameInput.value = updatedList.join('\n');
      }
    }
  }, 80);
};
</script>

<style scoped>
.picker-layout { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
textarea { width: 100%; height: 200px; padding: 10px; border-radius: 8px; border: 1px solid #ddd; }
.winner-box { 
  height: 150px; display: flex; align-items: center; justify-content: center; 
  font-size: 3rem; background: #f8f9fa; border: 4px dashed #42b983; border-radius: 12px; margin-bottom: 20px;
  color: #2c3e50; transition: transform 0.1s;
}
.winner-box.spinning { transform: scale(1.05); background: #e8f5e9; }
.btn-primary { 
  width: 100%; padding: 15px; font-size: 1.2rem; background: #42b983; color: white; border: none; border-radius: 8px; cursor: pointer;
}
.btn-primary:disabled { background: #ccc; }
</style>