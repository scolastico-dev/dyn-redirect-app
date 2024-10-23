<template>
  <div class="min-h-screen bg-black p-4">
    <main-screen
      v-if="view === 'main'"
      :settings="settings"
      @save="save($event)"
      @open="view = 'settings'"
    />
    <settings-screen
      v-else-if="view === 'settings'"
      :settings="settings"
      @save="save($event)"
      @close="view = 'main'"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import MainScreen from './components/MainScreen.vue'
import SettingsScreen from './components/SettingsScreen.vue'
import './assets/main.css'

const STORAGE_NAME = 'dyn-redirect-settings'

const rawData = localStorage.getItem(STORAGE_NAME)
const view = ref('main')
const settings = ref(
  rawData
    ? JSON.parse(rawData)
    : {
        profiles: [
          {
            name: 'Profile 1',
            url: 'https://',
            secret: '',
          },
        ],
        history: [],
      },
)

function save(data) {
  settings.value = data
  localStorage.setItem(STORAGE_NAME, JSON.stringify(data))
}

save(settings.value)
</script>
