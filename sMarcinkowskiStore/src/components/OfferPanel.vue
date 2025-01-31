<template>
  <div
    class="offer-panel"
    :class="{ open: isOpen }"
    @mouseenter="openPanel"
    @mouseleave="closePanel"
  >
    <div class="content">
      <!-- Tutaj wstaw treść swojej oferty -->
      <h1>Moja Oferta</h1>
    </div>
    <!-- Półokrągły przycisk wystający na zewnątrz panelu -->
    <button class="toggle-button" @click="toggle">
      <span class="arrow">←</span>
    </button>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  }
})
const emit = defineEmits(['update:isOpen'])

function toggle() {
  emit('update:isOpen', !props.isOpen)
}

function openPanel() {
  emit('update:isOpen', true)
}

function closePanel() {
  emit('update:isOpen', false)
}
</script>

<style scoped>
.offer-panel {
  position: absolute;
  top: 0;
  left: 0;
  width: 90%;
  height: 100%;
  background-color: rgba(255, 255, 255, 0.95);
  transform: translateX(-85%);
  transition: transform 0.5s ease;
  z-index: 10;
}

.offer-panel.open {
  transform: translateX(0);
}

.content {
  padding: 20px;
}

/* Półokrągły przycisk wystający na zewnątrz panelu */
.toggle-button {
  position: absolute;
  top: 50%;
  right: -25px; /* przycisk wystaje poza panel */
  transform: translateY(-50%);
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
}

.arrow {
  font-size: 1.5rem;
  /* Obrót można modyfikować w zależności od efektu – obecnie strzałka wskazuje w lewo */
  transform: rotate(180deg);
}
</style>
