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
import { onMounted, ref } from 'vue'
import ParallaxPanel from './ParallaxPanel.vue'
import front from '../assets/front_1.svg'
import front2 from '../assets/front_2.svg'
import clouds1 from '../assets/clouds_1.svg'
import clouds2 from '../assets/clouds_2.svg'
import sky from '../assets/sky.svg'
import sun from '../assets/sun.svg'

const panelHovered = ref(false)
const isMobile = ref(false)
const parallaxLayers = ref([
  { image: front, speed: 0, layer: 4 },
  { image: front2, speed: 0.3, layer: 3 },
  { image: clouds1, speed: 0.7, layer: 2 },
  { image: clouds2, speed: 0.5, layer: 2 },
  { image: sun, speed: 0.6, layer: 1 },
  { image: sky, speed: 0.2, layer: 0 },
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
