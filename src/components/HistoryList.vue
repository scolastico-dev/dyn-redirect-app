<!-- SwipeList.vue -->
<template>
  <div>
    <HistoryItem
      v-for="url in settings.history"
      :key="url"
      :url="url"
      @select="select"
      @delete="deleteItem"
    />
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'
import HistoryItem from './HistoryItem.vue'

defineProps({
  settings: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['select', 'delete'])

function select(url) {
  emit('select', url)
}

function deleteItem(url) {
  emit('delete', url)
}
</script>
