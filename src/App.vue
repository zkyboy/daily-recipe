<template>
  <div class="app">
    <header class="header">
      <div class="header-content">
        <h1>🤰 孕晚期控糖食谱</h1>
        <p class="subtitle">低GI · 控糖 · 高营养密度 · 优质蛋白 · 少食多餐</p>
        <p class="today">{{ todayDisplay }}</p>
      </div>
    </header>

    <main class="main">
      <!-- 今日概览 -->
      <section class="section">
        <h2>
          <span class="section-icon">📋</span>
          今日菜单 · {{ todayMeals.day }}
        </h2>
        <p class="tip">🤰 两餐之间可加餐：一小把坚果 / 半根黄瓜 / 1个番茄 / 无糖酸奶 / 低GI水果</p>

        <div class="meal-list">
          <div
            v-for="meal in todayMeals.meals"
            :key="meal.id"
            class="meal-card"
            :class="{ active: selectedMeal && selectedMeal.id === meal.id }"
            @click="selectMeal(meal)"
          >
            <div class="meal-header">
              <span class="meal-label">{{ meal.label }}</span>
              <div class="meal-tags">
                <span v-for="tag in meal.tags" :key="tag" class="tag">{{ tag }}</span>
              </div>
            </div>
            <h3 class="meal-name">{{ meal.name }}</h3>
            <div class="meal-icons">
              <span>🔥 {{ meal.nutrition.calories }}</span>
              <span>🌾 {{ meal.nutrition.carbs }}</span>
              <span>🥩 {{ meal.nutrition.protein }}</span>
              <span>🧈 {{ meal.nutrition.fat }}</span>
            </div>
          </div>
        </div>
      </section>

      <!-- 菜品详情 (展开时显示) -->
      <transition name="slide">
        <section v-if="selectedMeal" class="section detail-section">
          <div class="detail-header">
            <h2>
              <span class="section-icon">👨‍🍳</span>
              {{ selectedMeal.name }}
            </h2>
            <button class="close-btn" @click="selectedMeal = null">✕</button>
          </div>

          <div class="detail-content">
            <!-- 营养信息 -->
            <div class="nutrition-grid">
              <div class="nutrition-item">
                <span class="num">{{ selectedMeal.nutrition.calories }}</span>
                <span class="label">热量</span>
              </div>
              <div class="nutrition-item">
                <span class="num">{{ selectedMeal.nutrition.carbs }}</span>
                <span class="label">碳水</span>
              </div>
              <div class="nutrition-item">
                <span class="num">{{ selectedMeal.nutrition.protein }}</span>
                <span class="label">蛋白质</span>
              </div>
              <div class="nutrition-item">
                <span class="num">{{ selectedMeal.nutrition.fat }}</span>
                <span class="label">脂肪</span>
              </div>
            </div>

            <!-- 食材 -->
            <div class="ingredients">
              <h3>🛒 食材</h3>
              <ul>
                <li v-for="item in selectedMeal.ingredients" :key="item">{{ item }}</li>
              </ul>
            </div>

            <!-- 步骤 -->
            <div class="steps">
              <h3>📝 制作步骤</h3>
              <ol>
                <li v-for="(step, i) in selectedMeal.steps" :key="i">
                  <span class="step-num">{{ i + 1 }}</span>
                  {{ step }}
                </li>
              </ol>
            </div>
          </div>
        </section>
      </transition>

      <!-- 本周预览 -->
      <section class="section week-preview">
        <h2>
          <span class="section-icon">📅</span>
          本周菜单预览
        </h2>
        <div class="week-grid">
          <div
            v-for="day in weekMeals"
            :key="day.day"
            class="day-card"
            :class="{ 'is-today': day.day === todayMeals.day }"
          >
            <div class="day-name">{{ day.day }}</div>
            <div
              v-for="meal in day.meals"
              :key="meal.id"
              class="day-meal"
              @click="onWeekMealClick(day, meal)"
            >
              <span class="meal-icon">{{ meal.label.slice(0, 2) }}</span>
              <span class="meal-text">{{ meal.name }}</span>
            </div>
          </div>
        </div>
      </section>
    </main>

    <footer class="footer">
      <p>✨ 孕晚期控糖原则：低GI · 控糖 · 高营养密度 · 优质蛋白 · 少食多餐</p>
      <p class="footer-tip">每天记得喝水 1.5-2L，促进代谢 ✨</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import weekMeals from './data/meals.js'

const selectedMeal = ref(null)
const dayNames = ['周日', '周一', '周二', '周三', '周四', '周五', '周六']

const todayIndex = computed(() => {
  const jsDay = new Date().getDay()          // 0=周日, 1=周一, ..., 6=周六
  return (jsDay + 6) % 7                      // 转为 0=周一, ..., 5=周六, 6=周日
})
const todayDisplay = computed(() => {
  const date = new Date()
  const dayName = dayNames[new Date().getDay()]
  return `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日 ${dayName}`
})
const todayMeals = computed(() => weekMeals[todayIndex.value])

function selectMeal(meal) {
  selectedMeal.value = meal
}

function onWeekMealClick(day, meal) {
  selectedMeal.value = meal
}
</script>

<style>
:root {
  --green: #4caf50;
  --green-dark: #388e3c;
  --green-light: #e8f5e9;
  --orange: #ff9800;
  --text: #333;
  --text-light: #666;
  --bg: #fafaf5;
  --card-bg: #fff;
  --shadow: 0 2px 12px rgba(0,0,0,0.08);
  --radius: 14px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  background: var(--bg);
  color: var(--text);
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

.app {
  max-width: 800px;
  margin: 0 auto;
  padding: 0 16px 40px;
}

/* Header */
.header {
  background: linear-gradient(135deg, var(--green) 0%, #66bb6a 100%);
  color: white;
  margin: 0 -16px;
  padding: 40px 20px 32px;
  text-align: center;
}

.header-content h1 {
  font-size: 28px;
  margin-bottom: 6px;
}

.subtitle {
  font-size: 14px;
  opacity: 0.9;
  margin-bottom: 4px;
}

.today {
  font-size: 15px;
  margin-top: 8px;
  opacity: 0.95;
  font-weight: 500;
}

/* Sections */
.section {
  background: var(--card-bg);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 20px;
  margin-top: 16px;
}

.section h2 {
  font-size: 18px;
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  gap: 6px;
}

.section-icon {
  font-size: 22px;
}

.tip {
  font-size: 13px;
  color: var(--text-light);
  background: #fff8e1;
  padding: 10px 14px;
  border-radius: 8px;
  margin-bottom: 16px;
  line-height: 1.5;
}

/* Meal Cards */
.meal-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.meal-card {
  cursor: pointer;
  border: 2px solid transparent;
  border-radius: 12px;
  padding: 16px;
  background: #f9faf5;
  transition: all 0.2s;
}

.meal-card:hover {
  border-color: var(--green);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(76, 175, 80, 0.15);
}

.meal-card.active {
  border-color: var(--green);
  background: var(--green-light);
}

.meal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 8px;
}

.meal-label {
  font-weight: 700;
  font-size: 15px;
  color: var(--green-dark);
}

.meal-tags {
  display: flex;
  gap: 4px;
  flex-wrap: wrap;
}

.tag {
  font-size: 11px;
  background: var(--green-light);
  color: var(--green-dark);
  padding: 2px 8px;
  border-radius: 10px;
  font-weight: 500;
}

.meal-name {
  font-size: 18px;
  margin-bottom: 8px;
  line-height: 1.4;
}

.meal-icons {
  display: flex;
  gap: 12px;
  font-size: 12px;
  color: var(--text-light);
  flex-wrap: wrap;
}

/* Detail Section */
.detail-section {
  border: 2px solid var(--green);
}

.detail-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.detail-header h2 {
  margin-bottom: 0;
  flex: 1;
  font-size: 18px;
}

.close-btn {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: none;
  background: #f0f0f0;
  cursor: pointer;
  font-size: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s;
  flex-shrink: 0;
}

.close-btn:hover {
  background: #e0e0e0;
}

.nutrition-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  margin-bottom: 20px;
}

.nutrition-item {
  text-align: center;
  padding: 10px 6px;
  background: #f5faf5;
  border-radius: 10px;
}

.nutrition-item .num {
  display: block;
  font-size: 13px;
  font-weight: 700;
  color: var(--green-dark);
}

.nutrition-item .label {
  display: block;
  font-size: 11px;
  color: var(--text-light);
  margin-top: 2px;
}

.ingredients, .steps {
  margin-bottom: 20px;
}

.ingredients h3, .steps h3 {
  font-size: 16px;
  margin-bottom: 10px;
}

.ingredients ul {
  list-style: none;
}

.ingredients li {
  padding: 6px 0;
  padding-left: 20px;
  position: relative;
  font-size: 14px;
  border-bottom: 1px dashed #f0f0f0;
}

.ingredients li::before {
  content: '●';
  position: absolute;
  left: 0;
  color: var(--green);
  font-size: 10px;
  top: 10px;
}

.steps ol {
  list-style: none;
  counter-reset: step;
}

.steps li {
  display: flex;
  gap: 12px;
  padding: 10px 0;
  border-bottom: 1px dashed #f0f0f0;
  font-size: 14px;
  line-height: 1.6;
}

.step-num {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: var(--green);
  color: white;
  font-size: 13px;
  font-weight: 700;
  flex-shrink: 0;
}

/* Week Preview */
.week-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 12px;
}

.day-card {
  padding: 14px;
  border-radius: 12px;
  background: #f9faf5;
  border: 2px solid transparent;
}

.day-card.is-today {
  border-color: var(--green);
  background: var(--green-light);
}

.day-name {
  font-weight: 700;
  font-size: 15px;
  margin-bottom: 8px;
  color: var(--green-dark);
}

.day-meal {
  display: flex;
  gap: 8px;
  padding: 6px 0;
  cursor: pointer;
  font-size: 13px;
  transition: color 0.2s;
  align-items: center;
}

.day-meal:hover {
  color: var(--green);
}

.meal-icon {
  width: 28px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: white;
  border-radius: 8px;
  font-size: 12px;
  flex-shrink: 0;
  border: 1px solid #eee;
}

.meal-text {
  line-height: 1.3;
}

/* Transition */
.slide-enter-active, .slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter-from, .slide-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

/* Footer */
.footer {
  text-align: center;
  padding: 40px 20px 20px;
  color: var(--text-light);
  font-size: 13px;
}

.footer-tip {
  margin-top: 6px;
  font-size: 13px;
}

/* Responsive */
@media (max-width: 640px) {
  .nutrition-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .week-grid {
    grid-template-columns: 1fr;
  }

  .header {
    padding: 28px 16px 24px;
  }

  .header-content h1 {
    font-size: 22px;
  }

  .meal-name {
    font-size: 16px;
  }

  .meal-icons {
    gap: 8px;
    font-size: 11px;
  }
}
</style>
