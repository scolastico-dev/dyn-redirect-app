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
import { ref, onMounted } from 'vue'
import { Preferences } from '@capacitor/preferences'
import MainScreen from './components/MainScreen.vue'
import SettingsScreen from './components/SettingsScreen.vue'
import './assets/main.css'

const STORAGE_NAME = 'dyn-redirect-settings'

const view = ref('main')
const settings = ref({
  profiles: [
    {
      name: 'Profile 1',
      url: 'https://',
      secret: '',
    },
  ],
  history: [],
  selectedProfile: 0,
})

onMounted(() => {
  Preferences.get({ key: STORAGE_NAME }).then(result => {
    if (result.value) {
      settings.value = JSON.parse(result.value)
    } else {
      save(settings.value)
    }
  })
})

function save(data) {
  settings.value = data
  Preferences.set({ key: STORAGE_NAME, value: JSON.stringify(data) })
}
</script>
