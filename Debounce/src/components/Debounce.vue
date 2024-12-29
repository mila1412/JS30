<template>
  <input v-model="word" type="text" />
  <ul>
    <li v-for="(item, index) in filteredItems" :key="index">{{ item }}</li>
  </ul>
</template>

<script setup>
import { ref, watch } from 'vue'

const word = ref('')
const list = ['apple', 'array', 'article', 'album', 'artist', 'author']
let filteredItems = ref([])

watch(word, (newVal) => {
  if (newVal) {
    debounceFilter(newVal)
  } else {
    filteredItems.value = []
  }
})

const debounceFilter = debounce((val) => {
  filteredItems.value = list.filter((item) => item.toLowerCase().includes(val.toLowerCase()))
}, 1000)

function debounce(func, delay = 500) {
  let timeout
  return (...args) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
      func(...args)
    }, delay)
  }
}
</script>
