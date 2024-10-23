<template>
  <div
    class="my-2 relative overflow-hidden swipeable"
    @touchstart.passive="handleStart"
    @touchmove.passive="handleMove"
    @touchend="handleEnd"
    @mousedown="handleStart"
    @mousemove="handleMove"
    @mouseup="handleEnd"
    @mouseleave="handleEnd"
  >
    <div
      class="absolute inset-0 flex items-center justify-end px-4 pointer-events-none"
    >
      <span :style="textStyle" class="text-white">Delete</span>
    </div>

    <!-- Swipeable button -->
    <button
      @click="onSelect"
      :style="buttonStyle"
      class="w-full text-left py-3 px-4 border border-white text-white truncate transition-transform duration-200 ease-out"
    >
      {{ url }}
    </button>
  </div>
</template>

<script setup>
import { reactive, computed, ref } from 'vue'

const props = defineProps({
  url: {
    type: String,
    required: true,
  },
})

const emit = defineEmits(['select', 'delete'])

const data = reactive({
  startX: 0,
  translateX: 0,
  dragging: false,
  buttonWidth: 0,
  deleted: false,
})

const isTicking = ref(false)
let lastTranslateX = 0
let animationFrame = null

const swipeProgress = computed(() => {
  return Math.min(Math.abs(data.translateX) / (data.buttonWidth || 1), 1)
})

const buttonStyle = computed(() => {
  const opacity = swipeProgress.value
  return {
    transform: `translateX(${data.translateX}px)`,
    backgroundColor: `rgba(255, 0, 0, ${opacity * 0.5})`,
  }
})

const textStyle = computed(() => {
  const opacity = swipeProgress.value * 1.5
  return {
    opacity: opacity,
    transition: 'opacity 0.2s ease-out',
  }
})

function onSelect() {
  if (!data.deleted && data.translateX === 0) {
    emit('select', props.url)
  }
}

function handleStart(event) {
  if (data.deleted) return
  data.dragging = true
  data.startX = event.type.startsWith('touch')
    ? event.touches[0].clientX
    : event.clientX
  data.buttonWidth = event.currentTarget.offsetWidth

  // Cancel any existing animation frame to avoid conflicts
  if (animationFrame) {
    cancelAnimationFrame(animationFrame)
    animationFrame = null
  }
}

function handleMove(event) {
  if (!data.dragging) return

  const currentX = event.type.startsWith('touch')
    ? event.touches[0].clientX
    : event.clientX
  let deltaX = currentX - data.startX

  if (deltaX > 0) {
    deltaX = 0
  }

  lastTranslateX = deltaX

  if (!isTicking.value) {
    isTicking.value = true
    animationFrame = requestAnimationFrame(() => {
      data.translateX = lastTranslateX
      isTicking.value = false
    })
  }
}

function handleEnd() {
  if (!data.dragging) return
  data.dragging = false

  const threshold = data.buttonWidth * 0.5

  if (Math.abs(data.translateX) > threshold) {
    data.translateX = -data.buttonWidth
    setTimeout(() => {
      data.deleted = true
      emit('delete', props.url)
    }, 200)
  } else {
    // Reset translateX with a transition
    data.translateX = 0
    setTimeout(() => {
      data.dragging = false // Explicitly reset dragging
    }, 200)
  }

  // Cancel any pending animation frame
  if (animationFrame) {
    cancelAnimationFrame(animationFrame)
    animationFrame = null
    isTicking.value = false
  }
}
</script>

<style scoped>
button {
  position: relative;
  z-index: 2; /* Ensure the button stays on top */
}

.relative {
  position: relative;
}

.swipeable {
  touch-action: pan-y; /* Allow vertical scrolling */
  touch-action: none;
}
</style>
