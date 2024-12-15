<template>
  <div class="todo-container">
    <ul class="todo-list">
      <li v-for="(item, index) in list" :key="item.id" class="todo-item">
        <input
          v-model="item.isChecked"
          type="checkbox"
          @change="(e) => handleCheckChange(e, index, item)"
        />
        <span :class="{ checked: item.isChecked }">
          {{ item.text }}
        </span>
      </li>
    </ul>
    <div class="debug-info">
      <h3>調試信息：</h3>
      <pre>{{ debugInfo }}</pre>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const list = ref([
  { id: 1, text: '任務 1', isChecked: false },
  { id: 2, text: '任務 2', isChecked: false },
  { id: 3, text: '任務 3', isChecked: false },
  { id: 4, text: '任務 4', isChecked: false },
  { id: 5, text: '任務 5', isChecked: false },
])

const debugInfo = ref({
  lastAction: null,
  selectedIndexes: [],
})

// 記錄上一次選中的索引
let lastCheckedIndex = -1

// 處理 checkbox 變化
const handleCheckChange = (event, currentIndex, item) => {
  const isShiftKey = event.shiftKey

  if (isShiftKey && lastCheckedIndex !== -1) {
    // 計算要選中的範圍
    const start = Math.min(lastCheckedIndex, currentIndex)
    const end = Math.max(lastCheckedIndex, currentIndex)

    // 將範圍內的所有項目設置為當前項目的狀態
    for (let i = start; i <= end; i++) {
      list.value[i].isChecked = item.isChecked
    }

    // 更新調試信息
    debugInfo.value.lastAction = {
      type: 'shift-select',
      range: { start, end },
      status: item.isChecked,
    }

    debugInfo.value.selectedIndexes = list.value
      .map((item, index) => (item.isChecked ? index : -1))
      .filter((index) => index !== -1)
  } else {
    // 更新調試信息
    debugInfo.value.lastAction = {
      type: 'single-select',
      index: currentIndex,
      status: item.isChecked,
    }
  }

  // 更新最後選中的索引
  lastCheckedIndex = currentIndex
}

// 監聽列表變化
watch(
  list,
  (newVal) => {
    debugInfo.value.selectedIndexes = newVal
      .map((item, index) => (item.isChecked ? index : -1))
      .filter((index) => index !== -1)
  },
  { deep: true },
)
</script>

<style scoped>
.todo-container {
  padding: 20px;
}

.todo-list {
  list-style: none;
  padding: 0;
}

.todo-item {
  margin: 10px 0;
}

.checked {
  text-decoration: line-through;
  color: #666;
}

.debug-info {
  margin-top: 20px;
  padding: 10px;
  background: #f5f5f5;
  border-radius: 4px;
}
</style>
