<template>
  <profile-select
    ref="selectRef"
    :settings="settings"
    @change="setProfile($event)"
  />
  <div class="flex justify-end gap-2">
    <button
      v-for="(action, index) in profileActions"
      :key="index"
      @click="action.handler"
      class="btn-action"
    >
      {{ action.label }}
    </button>
  </div>
  <div class="border-white border p-2 grid grid-cols-[auto,1fr] gap-2 mt-4">
    <template v-for="(field, index) in profileFields" :key="index">
      <label :for="field.id" class="text-white">{{ field.label }}:</label>
      <input :id="field.id" class="input-field" v-model="curr[field.model]" />
    </template>
  </div>
  <div class="flex justify-end gap-2">
    <button
      v-for="(action, index) in formActions"
      :key="index"
      @click="action.handler"
      class="btn-action"
    >
      {{ action.label }}
    </button>
  </div>
  <div class="flex justify-end gap-2">
    <button @click="showLicense = true" class="btn-action">
      Open Licenses
    </button>
  </div>
  <license-screen v-if="showLicense" @close="showLicense = false" />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import ProfileSelect from './ProfileSelect.vue'
import LicenseScreen from './LicenseScreen.vue'

const props = defineProps(['settings'])
const emit = defineEmits(['save', 'close'])
const profile = ref(0)
const curr = ref({})
const selectRef = ref()
const showLicense = ref(false)

onMounted(() => {
  curr.value = { ...props.settings.profiles[profile.value] }
})

function setProfile(index) {
  if (index === -1) emit('close')
  else {
    profile.value = index
    curr.value = { ...props.settings.profiles[index] }
  }
}

function addProfile() {
  emit('save', {
    ...props.settings,
    profiles: [
      ...props.settings.profiles,
      { name: 'New Profile', url: 'https://', secret: '' },
    ],
  })
  selectRef.value.setProfile(props.settings.profiles.length)
}

function removeProfile() {
  if (props.settings.profiles.length === 1) {
    alert('Cannot remove last profile')
    return
  }
  emit('save', {
    ...props.settings,
    profiles: props.settings.profiles.filter((_, i) => i !== profile.value),
  })
  selectRef.value.setProfile(0)
}

function resetHistory() {
  emit('save', { ...props.settings, history: [] })
  alert('History reset!')
}

function save() {
  emit('save', {
    ...props.settings,
    profiles: props.settings.profiles.map((p, i) =>
      i === profile.value ? curr.value : p,
    ),
  })
  alert('Saved!')
}

const profileActions = [
  { label: 'Add Profile', handler: addProfile },
  { label: 'Remove Profile', handler: removeProfile },
]

const formActions = [
  { label: 'Reset History', handler: resetHistory },
  { label: 'Close', handler: () => setProfile(-1) },
  { label: 'Save', handler: save },
]

const profileFields = [
  { id: 'name', label: 'Profile Name', model: 'name' },
  { id: 'url', label: 'URL', model: 'url' },
  { id: 'secret', label: 'Secret', model: 'secret' },
]
</script>

<style scoped>
.btn-action {
  @apply border-white border text-white font-medium py-1 px-2 mt-4;
}

.input-field {
  @apply w-full bg-transparent border-b-2 border-white/40 text-white active:outline-0;
}
</style>
