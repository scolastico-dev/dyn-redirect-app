<template>
  <div class="fixed inset-0 p-4 bg-black text-white overflow-y-auto">
    <div class="flex justify-end mb-4">
      <button class="py-1 px-2 border-white border" @click="$emit('close')">
        Close
      </button>
    </div>
    <p class="whitespace-pre-wrap">{{ license }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
defineEmits(['close'])
const license = ref('')
onMounted(async () => {
  const { data } = await axios.get('/licenses.txt', { responseType: 'text' })
  license.value = data
})
</script>
