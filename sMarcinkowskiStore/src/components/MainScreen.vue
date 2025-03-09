<template>
  <div class="container">
    <ParallaxPanel
      :layers="parallaxLayers"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
      <h2>Parallax</h2>
    </ParallaxPanel>
    <ParallaxPanel
      :layers="parallaxLayers"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
      <h2>Parallax</h2>
    </ParallaxPanel>
    <main>s</main>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import ParallaxPanel from './ParallaxPanel.vue'

import clouds1 from '@/assets/clouds1.png'
import clouds2 from '@/assets/clouds2.png'

import front from '@/assets/jula/Drzewo 1.svg'
import front2 from '@/assets/jula/Mountain_front_1.svg'

const panelHovered = ref(false)
const isMobile = ref(false)
const parallaxLayers = ref([
  { image: front, speed: 0, parallaxX: true, parallaxY: true, layer: 10, direction: 1 },
  { image: front2, speed: 0.3, parallaxX: true, parallaxY: true, layer: 0, direction: -1 },
  { image: clouds1, speed: 0.7, parallaxX: true, parallaxY: false, layer: 2, direction: 1 },
  { image: clouds2, speed: 0.5, parallaxX: false, parallaxY: true, layer: 3, direction: -1 },
])


onMounted(() => {
  isMobile.value = window.innerWidth < 768
  window.addEventListener('resize', () => {
    isMobile.value = window.innerWidth < 768
  })
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
  min-height: 100vh;
  min-width: 100vw;
  z-index: 0;
  transition: transform 0.5s ease;
}
</style>
