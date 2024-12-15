<template>
  <input v-model="newItem" @keydown.enter="add" type="text" />
  <button @click.prevent="add">ADD TODO</button>
  <ul v-for="(item, index) in list" :key="item.id">
    <li>
      <input v-model="item.isChecked" @change="handleCheckChange(index)" type="checkbox" />
      <span>{{ item.text + item.id }}</span>
      <button @click.prevent="remove(item.id)">REMOVE</button>
    </li>
  </ul>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const newItem = ref('')
const list = ref([{ id: 0, text: '第一個', isChecked: false }])

// watch(
//   list,
//   () => {
//   },
//   { deep: true },
// )

const isShiftKey = ref(false)
const handleKeyDown = (e) => {
  if (e.key === 'Shift') {
    isShiftKey.value = true
  }
}

const handleKeyUp = (e) => {
  if (e.key === 'Shift') {
    isShiftKey.value = false
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown)
  window.addEventListener('keyup', handleKeyUp)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown)
  window.removeEventListener('keyup', handleKeyUp)
})

const startIndex = ref(-1)
function handleCheckChange(index) {
  // 第一個跟最後一個之間都會被選取，不能分段
  // if (isShiftKey.value) {
  //   const start = list.value.findIndex((item) => item.isChecked)
  //   const end = list.value.findLastIndex((item) => item.isChecked)
  //   for (let i = start + 1; i < end; i++) {
  //     list.value[i].isChecked = true
  //   }
  // }

  if (isShiftKey.value && startIndex.value !== -1) {
    const start = Math.min(startIndex.value, index)
    const end = Math.max(startIndex.value, index)

    for (let i = start + 1; i < end; i++) {
      list.value[i].isChecked = true
    }
  }
  startIndex.value = index
}

function add() {
  if (!newItem.value.trim()) return
  list.value.push({ id: list.value.length + 1, text: newItem.value, isChecked: false })
  newItem.value = ''
}

function remove(itemId) {
  list.value.splice(
    list.value.findIndex((item) => item.id === itemId),
    1,
  )
}
</script>
