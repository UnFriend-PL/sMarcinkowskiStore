<template>
  <div ref="containerRef" class="parallax-container">
    <img v-for="(layer, index) in layers"
         :key="index"
         :src="layer.image"
         class="parallax-layer"
         :style="{ zIndex: layer.layer }"
         alt="Layer" />
  </div>
</template>

<script setup>
import {onMounted, ref} from 'vue'
import {gsap} from 'gsap'
import {ScrollTrigger} from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const props = defineProps({
  layers: {
    type: Array,
    required: true
  }
})

const containerRef = ref(null)

onMounted(() => {
  const containerEl = containerRef.value
  const layersEls = containerEl.querySelectorAll('.parallax-layer')
  const containerHeight = containerEl.offsetHeight

  const introTimeline = gsap.timeline({paused: true})
  props.layers.forEach((layer, i) => {
    const el = layersEls[i]
    const speed = layer.speed || 1
    const direction = layer.direction || 1
    const startOffset = 100 + 50 * speed
    introTimeline.fromTo(el,
      {
        y: startOffset,
        opacity: 0
      },
      {
        y: 0,
        opacity: 1,
        duration: 1.2 * (1 / speed),
        ease: 'power2.out'
      },
      0)
  })

  ScrollTrigger.create({
    trigger: containerEl,
    start: 'top bottom',
    onEnter: () => introTimeline.restart(),
    onEnterBack: () => introTimeline.restart()
  })

  props.layers.forEach((layer, i) => {
    const el = layersEls[i]
    const speed = layer.speed || 1
    const direction = layer.direction || 1
    const moveDistance = containerHeight * speed
    gsap.to(el, {
      scrollTrigger: {
        trigger: containerEl,
        start: 'top top',
        end: 'bottom top',
        scrub: true
      },
      y: -direction * moveDistance,
      ease: 'none'
    })
  })

  gsap.to(containerEl, {
    scrollTrigger: {
      trigger: containerEl,
      start: 'top center',
      end: 'bottom top',
      scrub: true
    },
    opacity: 0,
    ease: 'none'
  })
})
</script>

<style scoped>
.parallax-container {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: visible;
}

.parallax-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  will-change: transform;
}
</style>
