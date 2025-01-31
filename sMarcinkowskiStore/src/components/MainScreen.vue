<template>
  <div
    class="container"
    @mousemove="handleMouseMove"
    @touchstart="handleTouchStart"
    @touchmove="handleTouchMove"
  >
    <!-- Panel oferty -->
    <OfferPanel
      :isOpen="offerOpen"
      @update:isOpen="offerOpen = $event"
      @hover="isOfferHovered = true"
      @leave="isOfferHovered = false"
    />

    <!-- Główna zawartość opakowana w <main> -->
    <main :class="{ pushed: isOfferHovered }">
      <!-- Pętla przez wszystkie warstwy paralaksy -->
      <div
        v-for="(layer, index) in parallaxLayers"
        :key="index"
        class="parallax-layer"
        :style="getLayerStyle(layer, index)"
      ></div>
    </main>

    <!-- Kontrolki mobilne -->
    <div class="mobile-controls" v-if="isMobile">
      <button @click="toggleOffer('left')" class="mobile-button left">
        <i class="arrow-left"></i>
      </button>
      <button @click="toggleOffer('right')" class="mobile-button right">
        <i class="arrow-right"></i>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import OfferPanel from './OfferPanel.vue'

// Import przykładowych zasobów graficznych
import clouds1 from '@/assets/clouds1.png'
import clouds2 from '@/assets/clouds2.png'
import ground from '@/assets/ground.png'
import sky from '@/assets/sky.png'

const offerOpen = ref(false)
const isOfferHovered = ref(false)
const parallaxOffsetX = ref(0)
const parallaxOffsetY = ref(0)
const isMobile = ref(false)
const touchStartX = ref(0)

const parallaxLayers = ref([
  { image: sky, speed: 0 },
  { image: ground, speed: 0.3 },
  { image: clouds1, speed: 0.7 },
  { image: clouds2, speed: 0.5 },
])

onMounted(() => {
  isMobile.value = window.innerWidth < 768
  window.addEventListener('resize', () => {
    isMobile.value = window.innerWidth < 768
  })
})

function handleMouseMove(event) {
  const windowWidth = window.innerWidth
  const relativeX = (event.clientX - windowWidth / 2) / (windowWidth / 2)
  parallaxOffsetX.value = relativeX * 20

  const windowHeight = window.innerHeight
  const relativeY = (event.clientY - windowHeight / 2) / (windowHeight / 2)
  parallaxOffsetY.value = relativeY * 20

  const edgeThreshold = 50
  if (event.clientX < edgeThreshold) {
    offerOpen.value = true
  } else if (event.clientX > windowWidth - edgeThreshold) {
    offerOpen.value = false
  }
}

function handleTouchStart(event) {
  touchStartX.value = event.touches[0].clientX
}

function handleTouchMove(event) {
  const touchCurrentX = event.touches[0].clientX
  if (touchCurrentX - touchStartX.value > 50) {
    offerOpen.value = true
  }
}

function toggleOffer(direction) {
  if (direction === 'left') {
    offerOpen.value = true
  } else if (direction === 'right') {
    offerOpen.value = false
  }
}

function getLayerStyle(layer, index) {
  return {
    transform: `translate(${parallaxOffsetX.value * layer.speed}px, ${parallaxOffsetY.value * layer.speed}px)`,
    background: `url('${layer.image}') no-repeat center center`,
    backgroundSize: 'cover',
    position: 'absolute',
    top: 0,
    left: 0,
    width: '100%',
    height: '100%',
    zIndex: index,
    transition: 'transform 0.1s'
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

.parallax-layer {
  /* styles dynamics from getLayerStyle function */
}

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
