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
const STORAGE_NAME = 'dyn-redirect-selected-profile'
export default defineComponent({
  props: ['settings', 'specialText'],
  emits: ['change'],
  data: () => ({
    profile: 0,
  }),
  mounted() {
    const profile = Number(localStorage.getItem(STORAGE_NAME)) || 0
    if (profile < this.settings.profiles.length) this.setProfile(profile)
  },
  watch: {
    profile(val, old) {
      if (val === old) return
      if (val >= 0) localStorage.setItem(STORAGE_NAME, val)
      this.$emit('change', val)
    },
  },
  methods: {
    setProfile(index) {
      if (index < 0) index = 0
      this.profile = index
    },
  },
})
</script>
