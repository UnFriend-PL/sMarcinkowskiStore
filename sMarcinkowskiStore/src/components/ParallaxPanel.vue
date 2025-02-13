<template>
  <div
    class="parallax-panel"
    :class="positionClass"
    :style="panelStyle"
    @mousemove="handleMouseMove"
    @touchstart="handleTouchStart"
    @touchmove="handleTouchMove"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
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
import { ref, computed } from 'vue'
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
  activePanel: {
    type: String,
    default: null,
  },
  left: { type: Boolean, default: false },
  right: { type: Boolean, default: false },
  top: { type: Boolean, default: false },
  bottom: { type: Boolean, default: false },
  layers: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['update:isOpen', 'hover', 'leave'])

const parallaxOffsetX = ref(0)
const parallaxOffsetY = ref(0)
const touchStartX = ref(0)
const touchStartY = ref(0)

function handleMouseMove(event) {
  const rect = event.currentTarget.getBoundingClientRect()
  const relativeX = (event.clientX - (rect.left + rect.width / 2)) / (rect.width / 2)
  parallaxOffsetX.value = relativeX * 20

  const relativeY = (event.clientY - (rect.top + rect.height / 2)) / (rect.height / 2)
  parallaxOffsetY.value = relativeY * 20
}

function handleTouchStart(event) {
  touchStartX.value = event.touches[0].clientX
  touchStartX.value = event.touches[0].clientY
}

function handleTouchMove(event) {
  const touchCurrentX = event.touches[0].clientX;
  const touchCurrentY = event.touches[0].clientY;
  if (positionClass.value === 'left' && touchCurrentX - touchStartX.value > 50) {
    openPanel()
  } else if (positionClass.value === 'right' && touchStartX.value - touchCurrentX > 50) {
    openPanel()
  }
  else if (positionClass.value === 'top' && touchCurrentY - touchStartY.value > 50) {
    openPanel()
  } else if (positionClass.value === 'bottom' && touchStartY.value - touchCurrentY > 50) {
    openPanel()
  }
}

function handleMouseEnter() {
  emit('hover')
  openPanel()
}

function handleMouseLeave() {
  emit('leave')
  closePanel()
}

function openPanel() {
  emit('update:isOpen', true)
}

function closePanel() {
  emit('update:isOpen', false)
}

function toggle() {
  emit('update:isOpen', !props.isOpen)
}

const positionClass = computed(() => {
  if (props.left) return 'left'
  if (props.right) return 'right'
  if (props.top) return 'top'
  if (props.bottom) return 'bottom'
  return 'left'
})

const arrowRotation = computed(() => {
  const pos = positionClass.value
  switch (pos) {
    case 'left':
      return props.isOpen ? 'rotate(0deg)' : 'rotate(180deg)'
    case 'right':
      return props.isOpen ? 'rotate(180deg)' : 'rotate(0deg)'
    case 'top':
      return props.isOpen ? 'rotate(90deg)' : 'rotate(-90deg)'
    case 'bottom':
      return props.isOpen ? 'rotate(-90deg)' : 'rotate(90deg)'
    default:
      return 'rotate(180deg)'
  }
})

const panelTransform = computed(() => {
  const direction = positionClass.value
  if (props.activePanel && props.activePanel !== direction) {
    switch (direction) {
      case 'left': return 'translateX(-100%)'
      case 'right': return 'translateX(100%)'
      case 'top': return 'translateY(-100%)'
      case 'bottom': return 'translateY(100%)'
    }
  } else {
    if (props.isOpen) {
      if (direction === 'left' || direction === 'right') return 'translateX(0)'
      else return 'translateY(0)'
    } else {
      switch (direction) {
        case 'left': return 'translateX(-90%)'
        case 'right': return 'translateX(90%)'
        case 'top': return 'translateY(-90%)'
        case 'bottom': return 'translateY(90%)'
      }
    }
  }
})

const panelStyle = computed(() => ({
  transform: panelTransform.value,
  transition: 'transform 0.5s ease'
}))

function getLayerStyle(layer) {
  const translateX = layer.parallaxX ? parallaxOffsetX.value * layer.speed : 0
  const translateY = layer.parallaxY ? parallaxOffsetY.value * layer.speed : 0
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
    transition: 'transform 0.1s'
  }
}
</script>

<style scoped>
.parallax-panel {
  position: absolute;
  background-color: rgba(255, 255, 255, 0.95);
  z-index: 10;
  overflow-x: hidden;
  scrollbar-width: none; }

.parallax-panel.left {
  top: 0;
  left: 0;
  width: 85%;
  height: 100%;
}

.parallax-panel.right {
  top: 0;
  right: 0;
  width: 85%;
  height: 100%;
}

.parallax-panel.top {
  top: 0;
  left: 0;
  width: 100%;
  height: 85%;
}

.parallax-panel.bottom {
  bottom: 0;
  left: 0;
  width: 100%;
  height: 85%;
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

.toggle-button {
  position: absolute;
  width: 50px;
  height: 50px;
  border: none;
  background: #fff;
  border-radius: 50%;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 30;
}

.parallax-panel.left .toggle-button {
  top: 50%;
  right: -25px;
  transform: translateY(-50%);
}

.parallax-panel.right .toggle-button {
  top: 50%;
  left: -25px;
  transform: translateY(-50%);
}

.parallax-panel.top .toggle-button {
  left: 50%;
  bottom: -25px;
  transform: translateX(-50%);
}

.parallax-panel.bottom .toggle-button {
  left: 50%;
  top: -25px;
  transform: translateX(-50%);
}

.arrow {
  font-size: 1.5rem;
  display: inline-block;
  transition: transform 0.5s ease;
}

.parallax-layer {
}
</style>
