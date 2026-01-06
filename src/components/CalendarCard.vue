<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { Solar } from 'lunar-javascript'
import quotes from '../assets/quotes.json'
import footerImg from '../assets/footer_v2.png'
import paperBg from '../assets/paper.png'

const now = ref(new Date())
let timer = null

onMounted(() => {
  // Check every second to ensure we catch the midnight change immediately
  timer = setInterval(() => {
    const current = new Date()
    if (current.getDate() !== now.value.getDate()) {
      now.value = current
    }
  }, 1000)
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})

// Date parts
const day = computed(() => now.value.getDate().toString().padStart(2, '0'))
const monthCn = computed(() => {
  const months = ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月']
  return months[now.value.getMonth()]
})
const weekEn = computed(() => now.value.toLocaleDateString('en-US', { weekday: 'long' }))
const weekCn = computed(() => {
  const map = { 'Monday': '星期一', 'Tuesday': '星期二', 'Wednesday': '星期三', 'Thursday': '星期四', 'Friday': '星期五', 'Saturday': '星期六', 'Sunday': '星期日' }
  // Fallback if locale differs
  const days = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
  return days[now.value.getDay()]
})

// Lunar
const lunarDate = computed(() => {
  const solar = Solar.fromDate(now.value)
  const lunar = solar.getLunar()
  return `农历${lunar.getMonthInChinese()}月${lunar.getDayInChinese()}`
})

// Quote
const quote = computed(() => {
  const start = new Date(now.value.getFullYear(), 0, 0)
  const diff = now.value - start
  const oneDay = 1000 * 60 * 60 * 24
  const dayOfYear = Math.floor(diff / oneDay)
  return quotes[dayOfYear % quotes.length]
})
</script>

<template>
  <div class="calendar-card" :style="{ backgroundImage: `url(${paperBg})` }">
    <div class="header">
      <div class="left-date">
        <span class="day-number">{{ day }}</span>
        <div class="month-badge">
          <span>{{ monthCn }}</span>
        </div>
      </div>
      <div class="right-info">
        <div class="weekday">
          <span class="week-en">{{ weekEn }}</span>
          <span class="week-cn">{{ weekCn }}</span>
        </div>
        <div class="lunar-date">{{ lunarDate }}</div>
      </div>
    </div>

    <div class="quote-section">
      <p class="quote-text">{{ quote.text }}</p>
      <p class="quote-source">-- {{ quote.source }}</p>
    </div>

    <div class="footer-image">
      <img :src="footerImg" alt="Revolutionary Art" />
    </div>
  </div>
</template>

<style scoped>
.calendar-card {
  width: 380px;
  height: 600px; /* Fixed dimensions as requested */
  padding: 30px 20px 0 20px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
  border-radius: 4px;
  background-size: cover; /* Ensure texture covers the card */
  background-position: center;
}

/* Header */
.header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 2rem;
  border-bottom: 1px dashed var(--border-color); /* Visual tear-off line hint */
  padding-bottom: 10px;
}

.left-date {
  display: flex;
  align-items: flex-start;
}

.day-number {
  font-size: 8rem;
  line-height: 1;
  font-weight: bold;
  color: #8b3a3a; /* Muted red */
  font-family: "Anton", "Impact", "Arial Black", sans-serif;
  letter-spacing: 2px; /* Increased letter spacing for Anton */
}


.month-badge {
  writing-mode: vertical-rl;
  background-color: #8b3a3a;
  color: white;
  padding: 4px 2px;
  font-size: 1.2rem;
  font-weight: bold;
  margin-left: 10px;
  border-radius: 2px;
  height: fit-content;
  margin-top: 15px;
  letter-spacing: 2px;
}

.right-info {
  text-align: right;
  color: #555;
}

.weekday {
  display: flex;
  flex-direction: column;
  margin-bottom: 5px;
}

.week-en {
  font-size: 0.9rem;
  text-transform: uppercase;
  color: #888;
}

.week-cn {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
}

.lunar-date {
  font-size: 0.9rem;
  color: #888;
}

/* Quote */
.quote-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start; /* Left align like text usually is in books */
  padding: 0 10px;
}

.quote-text {
  font-size: 1.8rem; /* Increased size */
  line-height: 1.4;
  font-weight: bold;
  color: #333;
  margin-bottom: 1rem;
  text-align: justify;
  letter-spacing: -0.5px;
}

.quote-source {
  font-size: 1.1rem;
  color: #666;
  align-self: flex-start;
  font-style: italic;
  margin-left: 5px;
}

/* Footer */
.footer-image {
  margin: 0 -20px 0 -20px; /* Bleed to edges */
  height: 140px; /* Slightly taller to accommodate jagged edge */
  overflow: hidden;
  display: flex;
  align-items: flex-end;
  margin-top: auto;
  
  /* Irregular jagged top edge */
  clip-path: polygon(
    0% 10%, 
    5% 2%, 10% 9%, 15% 1%, 20% 10%, 
    25% 3%, 30% 11%, 35% 2%, 40% 10%, 
    45% 0%, 50% 9%, 55% 2%, 60% 11%, 
    65% 1%, 70% 10%, 75% 3%, 80% 11%, 
    85% 2%, 90% 10%, 95% 1%, 100% 12%, 
    100% 100%, 0% 100%
  );
  /* Fallback for non-supporting browsers? Unlikely needed for modern vue app */
}

.footer-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}


</style>
