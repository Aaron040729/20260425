<template>
  <div class="tool-section">
    <h2>🏆 小組競賽記分板</h2>
    <div class="controls">
      <input v-model="newGroupName" @keyup.enter="addGroup" placeholder="新增小組名稱">
      <button @click="addGroup">新增</button>
    </div>

    <transition-group name="flip-list" tag="div" class="group-list">
      <div v-for="group in sortedGroups" :key="group.id" class="group-card">
        <div class="group-info">
          <span class="rank">#{{ getRank(group) }}</span>
          <span class="name">{{ group.name }}</span>
        </div>
        <div class="score-controls">
          <span class="score-value">{{ group.score }}</span>
          <button @click="updateScore(group, 1)">+1</button>
          <button @click="updateScore(group, 5)">+5</button>
          <button @click="updateScore(group, -1)" class="btn-minus">-1</button>
        </div>
      </div>
    </transition-group>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';

const groups = ref([
  { id: 1, name: '第一組', score: 0 },
  { id: 2, name: '第二組', score: 0 }
]);
const newGroupName = ref('');

const sortedGroups = computed(() => {
  return [...groups.value].sort((a, b) => b.score - a.score);
});

const getRank = (group) => {
  return sortedGroups.value.findIndex(g => g.id === group.id) + 1;
};

const addGroup = () => {
  if (!newGroupName.value) return;
  groups.value.push({ id: Date.now(), name: newGroupName.value, score: 0 });
  newGroupName.value = '';
};

const updateScore = (group, amount) => {
  group.score += amount;
};

// 儲存至 LocalStorage
watch(groups, (newVal) => {
  localStorage.setItem('classroom-scores', JSON.stringify(newVal));
}, { deep: true });

onMounted(() => {
  const saved = localStorage.getItem('classroom-scores');
  if (saved) groups.value = JSON.parse(saved);
});
</script>

<style scoped>
.group-list { margin-top: 20px; }
.group-card { 
  display: flex; justify-content: space-between; align-items: center; 
  padding: 15px; margin-bottom: 10px; background: #fff; border: 1px solid #eee; border-radius: 10px;
}
.rank { font-weight: bold; color: #42b983; margin-right: 15px; }
.name { font-size: 1.2rem; font-weight: 500; }
.score-value { font-size: 1.5rem; font-weight: bold; margin-right: 15px; min-width: 40px; display: inline-block; }
.score-controls button { margin-left: 5px; padding: 5px 12px; border-radius: 4px; border: 1px solid #ddd; cursor: pointer; }
.btn-minus { background: #ffeded; color: #d63031; }
/* Vue 排序過渡效果 */
.flip-list-move { transition: transform 0.6s ease; }
</style>