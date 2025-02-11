<template>
  <div class="container">
    <ParallaxPanel
      :isOpen="openPanel === 'left'"
      left
      :layers="parallaxLayers"
      @update:isOpen="val => openPanel = val ? 'left' : (openPanel === 'left' ? null : openPanel)"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
    </ParallaxPanel>

    <ParallaxPanel
      :isOpen="openPanel === 'bottom'"
      bottom
      :layers="parallaxLayers"
      @update:isOpen="val => openPanel = val ? 'bottom' : (openPanel === 'bottom' ? null : openPanel)"
      @hover="panelHovered = true"
      @leave="panelHovered = false"
    >
    </ParallaxPanel>

    <main :class="{ pushed: panelHovered }">
    </main>

    <div class="mobile-controls" v-if="isMobile">
      <button @click="togglePanel('left')" class="mobile-button left">
        <i class="arrow-left"></i>
      </button>
      <button @click="togglePanel('right')" class="mobile-button right">
        <i class="arrow-right"></i>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import ParallaxPanel from './ParallaxPanel.vue'

import clouds1 from '@/assets/clouds1.png'
import clouds2 from '@/assets/clouds2.png'
import ground from '@/assets/ground.png'
import sky from '@/assets/sky.png'

const openPanel = ref(null) // null, 'left' lub 'right'
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

function togglePanel(direction) {
  if (direction === 'left') {
    openPanel.value = openPanel.value === 'left' ? null : 'left'
  } else if (direction === 'right') {
    openPanel.value = openPanel.value === 'right' ? null : 'right'
  }
  else if (direction === 'top') {
    openPanel.value = openPanel.value === 'top' ? null : 'top'
  }
  else if (direction === 'bottom') {
    openPanel.value = openPanel.value === 'bottom' ? null : 'bottom'
  }
}
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
  transition: transform 0.5s ease;
  z-index: 0;
}

main.pushed {
  transform: translateX(300px);
}

/* Kontrolki mobilne */
.mobile-controls {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 20px;
  z-index: 20;
}

.mobile-button {
  background: #fff;
  border: none;
  padding: 10px;
  border-radius: 50%;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  cursor: pointer;
}

.arrow-left::before {
  content: '←';
  font-size: 1.5rem;
}

.arrow-right::before {
  content: '→';
  font-size: 1.5rem;
}
</style>
