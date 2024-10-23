<template>
  <profile-select
    :settings="settings"
    @change="setProfile"
    specialText="Open Settings"
  />
  <input-box
    ref="inputRef"
    :settings="settings"
    :profile="profile"
    @submit="addToHistory"
  />
  <div class="my-4 border-b-2 border-white/40" />
  <history-list
    :settings="settings"
    @select="setUrl($event)"
    @delete="deleteFromHistory"
  />
</template>

<script setup>
import { ref } from 'vue'
import ProfileSelect from './ProfileSelect.vue'
import InputBox from './InputBox.vue'
import HistoryList from './HistoryList.vue'

const props = defineProps(['settings'])
const emit = defineEmits(['save', 'open'])
const profile = ref(0)
const inputRef = ref()

function addToHistory(url) {
  const history = props.settings.history
  const index = history.indexOf(url)
  if (index !== -1) history.splice(index, 1)
  history.unshift(url)
  if (history.length > 100) history.pop()
  emit('save', { ...props.settings, history })
}

function deleteFromHistory(url) {
  const history = props.settings.history
  const index = history.indexOf(url)
  if (index !== -1) history.splice(index, 1)
  emit('save', { ...props.settings, history })
}

function setUrl(url) {
  console.log(url)
  inputRef.value.setUrl(url)
}

function setProfile(index) {
  if (index === -1) emit('open')
  else profile.value = index
}
</script>
