<template>
  <div class="tool-section">
    <h2>📝 互動測驗系統</h2>
    
    <div v-if="!isSubmitted" class="exam-container">
      <div v-for="(q, index) in questions" :key="q.id" class="question-card">
        <p class="question-text">
          <span class="q-type">[{{ getTypeName(q.type) }}]</span> 
          {{ index + 1 }}. {{ q.text }}
        </p>
        
        <div class="options">
          <!-- 單選題或是非題 -->
          <template v-if="q.type === 'single' || q.type === 'boolean'">
            <label v-for="opt in q.options" :key="opt" class="option-label">
              <input type="radio" :name="'q'+q.id" :value="opt" v-model="userAnswers[index]">
              {{ opt }}
            </label>
          </template>

          <!-- 複選題 -->
          <template v-else-if="q.type === 'multiple'">
            <label v-for="opt in q.options" :key="opt" class="option-label">
              <input type="checkbox" :value="opt" v-model="userAnswers[index]">
              {{ opt }}
            </label>
          </template>
        </div>
      </div>

      <button @click="submitExam" class="btn-submit">提交測驗</button>
    </div>

    <!-- 結果回饋介面 -->
    <div v-else class="result-container">
      <div class="score-banner">
        <h3>測驗結果</h3>
        <div class="score-circle">{{ score }} / 100</div>
      </div>

      <div v-for="(q, index) in questions" :key="'res'+q.id" class="result-card" :class="isCorrect(index) ? 'correct' : 'wrong'">
        <p class="question-text">{{ index + 1 }}. {{ q.text }}</p>
        <p class="user-ans">你的答案：{{ formatAnswer(userAnswers[index]) }}</p>
        <p class="correct-ans">正確答案：{{ formatAnswer(q.answer) }}</p>
        <div class="explanation">
          <strong>解析：</strong> {{ q.explanation }}
        </div>
      </div>

      <button @click="resetExam" class="btn-secondary">重新作答</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue';

const questions = [
  {
    id: 1,
    type: 'single',
    text: 'Vue 3 中主要使用的 API 風格是什麼？',
    options: ['Options API', 'Composition API', 'Class API', 'Functional API'],
    answer: 'Composition API',
    explanation: 'Vue 3 引入了 Composition API 以提供更好的邏輯重用與程式碼組織。'
  },
  {
    id: 2,
    type: 'multiple',
    text: '以下哪些是 Vue 的核心指令？(複選)',
    options: ['v-if', 'v-for', 'v-model', 'v-class'],
    answer: ['v-if', 'v-for', 'v-model'],
    explanation: 'v-if, v-for, v-model 均為官方核心指令。v-class 並不存在，正確應為 :class。'
  },
  {
    id: 3,
    type: 'boolean',
    text: '在 Vue 中，v-model 可以實現雙向數據綁定。',
    options: ['正確', '錯誤'],
    answer: '正確',
    explanation: 'v-model 是 Vue 提供實現表單輸入與狀態雙向同步的語法糖。'
  }
];

// 初始化答案結構：複選題用陣列，其餘用字串
const userAnswers = ref(questions.map(q => q.type === 'multiple' ? [] : ''));
const isSubmitted = ref(false);
const score = ref(0);

const getTypeName = (type) => {
  const map = { single: '單選', multiple: '複選', boolean: '是非' };
  return map[type];
};

const formatAnswer = (ans) => Array.isArray(ans) ? ans.sort().join(', ') : ans;

const isCorrect = (index) => {
  const user = userAnswers.value[index];
  const correct = questions[index].answer;
  if (Array.isArray(correct)) {
    return JSON.stringify([...user].sort()) === JSON.stringify([...correct].sort());
  }
  return user === correct;
};

const submitExam = () => {
  let correctCount = 0;
  questions.forEach((q, index) => {
    if (isCorrect(index)) correctCount++;
  });
  score.value = Math.round((correctCount / questions.length) * 100);
  isSubmitted.value = true;
};

const resetExam = () => {
  userAnswers.value = questions.map(q => q.type === 'multiple' ? [] : '');
  isSubmitted.value = false;
  score.value = 0;
};
</script>

<style scoped>
.question-card, .result-card { 
  background: #fdfdfd; border: 1px solid #eee; border-radius: 8px; 
  padding: 20px; margin-bottom: 20px; text-align: left;
}
.question-text { font-size: 1.1rem; font-weight: bold; margin-bottom: 15px; }
.q-type { color: #42b983; font-size: 0.9rem; margin-right: 5px; }
.option-label { display: block; margin: 10px 0; cursor: pointer; }
.option-label input { margin-right: 10px; }
.btn-submit { width: 100%; padding: 12px; background: #42b983; color: white; border: none; border-radius: 6px; cursor: pointer; font-size: 1.1rem; }

.score-banner { text-align: center; margin-bottom: 30px; }
.score-circle { font-size: 3rem; font-weight: bold; color: #42b983; }
.result-card.correct { border-left: 5px solid #42b983; }
.result-card.wrong { border-left: 5px solid #e74c3c; }
.user-ans { color: #666; }
.correct-ans { color: #42b983; font-weight: bold; }
.explanation { margin-top: 10px; padding: 10px; background: #f0f9eb; border-radius: 4px; font-size: 0.9rem; }
.btn-secondary { padding: 10px 20px; background: #909399; color: white; border: none; border-radius: 4px; cursor: pointer; }
</style>