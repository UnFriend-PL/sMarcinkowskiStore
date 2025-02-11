<template>
  <div class="container">
    <ParallaxPanel
      :isOpen="openPanel === 'left'"
      left
      :activePanel="openPanel"
      :layers="parallaxLayers"
      @update:isOpen="val => openPanel = val ? 'left' : (openPanel === 'left' ? null : openPanel)"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
      <h2>Lewy panel</h2>
    </ParallaxPanel>

    <ParallaxPanel
      :isOpen="openPanel === 'right'"
      right
      :activePanel="openPanel"
      :layers="parallaxLayers"
      @update:isOpen="val => openPanel = val ? 'right' : (openPanel === 'right' ? null : openPanel)"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
      <h2>Prawy panel</h2>
    </ParallaxPanel>

    <main :style="mainTransformStyle">
      <h1>Główna zawartość</h1>
      <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
    </main>

  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import ParallaxPanel from './ParallaxPanel.vue'

import clouds1 from '@/assets/clouds1.png'
import clouds2 from '@/assets/clouds2.png'
import ground from '@/assets/ground.png'
import sky from '@/assets/sky.png'

const openPanel = ref(null) // null, 'left', 'right', 'top' lub 'bottom'
const panelHovered = ref(false)
const isMobile = ref(false)

const parallaxLayers = ref([
  { image: sky, speed: 0, parallaxX: true, parallaxY: true, layer: 0 },
  { image: ground, speed: 0.3, parallaxX: true, parallaxY: true, layer: 1 },
  { image: clouds1, speed: 0.7, parallaxX: true, parallaxY: false, layer: 2 },
  { image: clouds2, speed: 0.5, parallaxX: false, parallaxY: true, layer: 3 }
])

onMounted(() => {
  isMobile.value = window.innerWidth < 768
  window.addEventListener('resize', () => {
    isMobile.value = window.innerWidth < 768
  })
})


const mainTransformStyle = computed(() => {
  if (!openPanel.value) return {}
  let transformValue = ''
  switch (openPanel.value) {
    case 'left':
      transformValue = 'translateX(100vw)'
      break
    case 'right':
      transformValue = 'translateX(-100vw)'
      break
    case 'top':
      transformValue = 'translateY(100vh)'
      break
    case 'bottom':
      transformValue = 'translateY(-100vh)'
      break
    default:
      transformValue = ''
  }
  return {
    transform: transformValue,
    transition: 'transform 0.5s ease'
  }
})
</script>

<style scoped>
.container {
  position: relative;
  overflow: hidden;
  width: 100vw;
  height: 100vh;
}

main {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  transition: transform 0.5s ease
}
</style>
