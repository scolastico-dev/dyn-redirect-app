<template>
  <div class="text-xl text-right mt-2">
    <input
      v-model="url"
      class="w-full text-left py-1 px-2"
      :placeholder="current"
    />
    <button
      @click="submit()"
      class="border-white border text-white font-medium py-1 px-2 mt-2"
    >
      Submit
    </button>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'
export default defineComponent({
  props: ['settings', 'profile'],
  emits: ['submit'],
  data: () => ({
    url: '',
    current: 'loading...',
    timeout: null,
  }),
  mounted() {
    this.timeout = window.setTimeout(() => this.getCurrent(), 200)
  },
  watch: {
    profile() {
      window.clearTimeout(this.timeout)
      this.getCurrent()
    },
  },
  methods: {
    submit() {
      if (!this.url) return
      this.$emit('submit', this.url)
      const p = this.settings.profiles[this.profile]
      try {
        if (!p.secret) throw new Error('No secret')
        new URL(p.url)
      } catch {
        alert('Invalid Profile')
        return
      }
      axios.post(p.url, { url: this.url, secret: p.secret })
      this.current = this.url
      this.url = ''
    },
    setUrl(url) {
      this.url = url
    },
    async getCurrent() {
      const p = this.settings.profiles[this.profile]
      try {
        if (!p.secret) throw new Error('No secret')
        new URL(p.url)
      } catch {
        this.current = 'Invalid Profile'
        return
      }
      const res = await axios.get(p.url, {
        headers: { Accept: 'application/json' },
      })
      this.current = res.data.redirectUrl || ''
    },
  },
})
</script>
