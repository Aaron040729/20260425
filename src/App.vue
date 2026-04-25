<template>
  <div class="app-container">
    <header>
      <h1>Classroom Interactive Toolbox</h1>
      <nav class="nav-bar">
        <button @click="currentTab = 'Picker'" :class="{ active: currentTab === 'Picker' }">隨機抽籤</button>
        <button @click="currentTab = 'Scoreboard'" :class="{ active: currentTab === 'Scoreboard' }">小組計分</button>
        <button @click="currentTab = 'Timer'" :class="{ active: currentTab === 'Timer' }">計時器</button>
        <button @click="currentTab = 'Exam'" :class="{ active: currentTab === 'Exam' }">互動測驗</button>
      </nav>
    </header>

    <main class="content-area">
      <keep-alive>
        <component :is="tabs[currentTab]" />
      </keep-alive>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import RandomPicker from './components/RandomPicker.vue';
import Scoreboard from './components/Scoreboard.vue';
import Timer from './components/Timer.vue';
import ExamSystem from './components/ExamSystem.vue';

const currentTab = ref('Picker');
const tabs = {
  Picker: RandomPicker,
  Scoreboard: Scoreboard,
  Timer: Timer,
  Exam: ExamSystem
};
</script>

<style>
/* 全域樣式設定 */
body { font-family: 'PingFang TC', sans-serif; margin: 0; background-color: #f0f2f5; color: #333; }
.app-container { max-width: 900px; margin: 0 auto; padding: 20px; }
header { text-align: center; margin-bottom: 30px; }
h1 { color: #2c3e50; }
.nav-bar { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
.nav-bar button { 
  padding: 12px 24px; border: none; border-radius: 8px; cursor: pointer; 
  background: #fff; box-shadow: 0 2px 4px rgba(0,0,0,0.1); transition: 0.3s;
}
.nav-bar button.active { background: #42b983; color: white; transform: translateY(-2px); }
.content-area { 
  background: white; padding: 30px; border-radius: 12px; 
  box-shadow: 0 4px 12px rgba(0,0,0,0.05); min-height: 500px;
}
</style>
