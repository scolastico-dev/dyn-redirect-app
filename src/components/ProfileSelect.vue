<template>
  <div class="border-2 border-gray-300">
    <select
      class="bg-transparent text-white focus:outline-0 py-1 w-full"
      v-model.number="profile"
      placeholder="Search for a movie"
    >
      <option v-if="specialText" value="-1">{{ specialText }}</option>
      <option
        v-for="(profile, index) in settings.profiles"
        :key="index"
        :value="index"
        class="text-black bg-white"
      >
        {{ profile.name }}
      </option>
    </select>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import { Preferences } from '@capacitor/preferences'
const STORAGE_NAME = 'dyn-redirect-selected-profile'
export default defineComponent({
  props: ['settings', 'specialText'],
  emits: ['change'],
  data: () => ({
    profile: 0,
  }),
  mounted() {
    Preferences.get({ key: STORAGE_NAME }).then(result => {
      if (result.value) this.setProfile(parseInt(result.value))
    })
  },
  watch: {
    profile(val, old) {
      if (val === old) return
      if (val >= 0)
        Preferences.set({ key: STORAGE_NAME, value: val.toString() })
      this.$emit('change', val)
    },
  },
  methods: {
    setProfile(index) {
      if (index < 0) index = 0
      if (index >= this.settings.profiles.length) index = 0
      this.profile = index
    },
  },
})
</script>
