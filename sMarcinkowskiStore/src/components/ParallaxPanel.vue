<template>
  <div class="parallax-panel" :class="positionClass" :style="panelStyle">
    <div
      v-for="(layer, index) in layers"
      :key="index"
      class="parallax-layer"
      :style="getLayerStyle(layer)"
    ></div>

    <div class="panel-content-wrapper">
      <div class="panel-content">
        <slot>
          <h1>Domyślna treść</h1>
        </slot>
      </div>
    </div>

    <button class="toggle-button" @click="toggle">
      <span class="arrow" :style="{ transform: arrowRotation }">←</span>
    </button>
  </div>
</template>

<script setup>
import {ref, onMounted, onUnmounted} from 'vue'
import {defineProps, defineEmits} from 'vue'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
  layers: {
    type: Array,
    default: () => [],
  },
})

const emit = defineEmits(['update:isOpen'])

const scrollProgress = ref(0)

// Aktualizacja postępu scrolla
function handleScroll() {
  const scrollTop = window.scrollY
  const docHeight = document.documentElement.scrollHeight - window.innerHeight
  scrollProgress.value = docHeight ? scrollTop / docHeight : 0
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})
onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// Funkcja generująca styl warstwy wykorzystująca właściwość direction
function getLayerStyle(layer) {
  // Jeśli właściwość direction nie została podana, domyślnie ustawiamy 1
  const direction = layer.direction !== undefined ? layer.direction : 1
  const maxOffsetX = 200 // maksymalne przesunięcie poziome (do modyfikacji)
  const arcAmplitude = 50 // amplituda efektu łuku (do modyfikacji)

  const translateX = layer.parallaxX
    ? direction * scrollProgress.value * maxOffsetX * (layer.speed || 1)
    : 0
  const translateY = layer.parallaxY
    ? Math.sin(scrollProgress.value * Math.PI) * arcAmplitude * (layer.speed || 1)
    : 0

  return {
    transform: `translate(${translateX}px, ${translateY}px)`,
    background: `url('${layer.image}') no-repeat bottom center`,
    backgroundSize: 'contain',
    position: 'absolute',
    top: 0,
    left: 0,
    width: '102%',
    height: '102%',
    zIndex: layer.layer ?? 0,
    transition: 'transform 0.1s',
  }
}

function toggle() {
  emit('update:isOpen', !props.isOpen)
}
</script>

<style scoped>
.parallax-panel {
  position: absolute;
  min-width: 100vw;
  min-height: 100vh;
  background-color: rgba(255, 255, 255, 0.95);
  z-index: 10;
  overflow-x: hidden;
  scrollbar-width: none;
}

.panel-content-wrapper {
  overflow: hidden;
  width: 100%;
  height: 100%;
}

.panel-content {
  position: relative;
  z-index: 20;
  padding: 20px;
}

.parallax-layer {
  /* Dodatkowe style dla warstw, jeśli potrzebne */
}
</style>
