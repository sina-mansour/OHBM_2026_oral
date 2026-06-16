<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'
import { useMotion } from '@vueuse/motion'

type MotionState = Record<string, any>

const props = defineProps<{
  appearAt: number      // click number to appear
  disappearAt?: number  // click number to disappear (omit = stays visible)
  cx?: number           // center-x, % of slide width  (default 50)
  cy?: number           // center-y, % of slide height (default 50)
  w?: number            // width, % of slide width     (default 40)
  h?: number            // height, % of slide height   (omit = sized by content; cy is then top edge)
  enter?: MotionState
  leave?: MotionState
}>()

// Produces either a number or [start, end] array for v-click
const clickValue = computed(() =>
  props.disappearAt !== undefined ? [props.appearAt, props.disappearAt] : props.appearAt
)

const rootRef  = ref<HTMLElement | null>(null)
const motionRef = ref<HTMLElement | null>(null)
const isVisible = ref(false)

// Track visibility via the class Slidev toggles on v-click elements
let observer: MutationObserver | null = null
onMounted(() => {
  if (!rootRef.value) return
  isVisible.value = !rootRef.value.classList.contains('slidev-vclick-hidden')
  observer = new MutationObserver(() => {
    isVisible.value = !rootRef.value!.classList.contains('slidev-vclick-hidden')
  })
  observer.observe(rootRef.value, { attributes: true, attributeFilter: ['class'] })
  apply(isVisible.value
    ? { ...effectiveEnter.value, transition: { duration: 0 } }
    : { ...DEFAULT_INITIAL,      transition: { duration: 0 } }
  )
})
onUnmounted(() => observer?.disconnect())

// Positioning
const containerStyle = computed(() => {
  const cx = props.cx ?? 50
  const cy = props.cy ?? 50
  const w  = props.w  ?? 40
  const style: Record<string, string> = {
    left:  `${cx - w / 2}%`,
    width: `${w}%`,
  }
  if (props.h !== undefined) {
    style.top    = `${cy - props.h / 2}%`
    style.height = `${props.h}%`
  } else {
    // No explicit height: cy is the top edge, content determines height
    style.top = `${cy}%`
  }
  return style
})

// Motion — text exits to the left (same as BulletItem reverse-exit behaviour)
const DEFAULT_INITIAL: MotionState = { x: -50, opacity: 0 }
const effectiveEnter = computed<MotionState>(() =>
  props.enter ?? { x: 0, y: 0, scale: 1, opacity: 1, transition: { type: 'tween', duration: 500 } }
)
const effectiveLeave = computed<MotionState>(() =>
  props.leave ?? { x: -50, opacity: 0, transition: { type: 'tween', duration: 400 } }
)

const { apply } = useMotion(motionRef, { initial: DEFAULT_INITIAL })

watch(isVisible, (v) => {
  if (v) {
    apply(effectiveEnter.value)
  } else {
    apply(effectiveLeave.value)
    setTimeout(() => {
      if (!isVisible.value) apply({ ...DEFAULT_INITIAL, transition: { duration: 0 } })
    }, (effectiveLeave.value.transition?.duration ?? 400) + 50)
  }
})
</script>

<template>
  <!--
    v-click is bound internally so appearAt / disappearAt are self-contained.
    The root is the positioned container; the inner div carries the motion transform
    so it doesn't conflict with the absolute positioning of the outer div.
  -->
  <div
    ref="rootRef"
    v-click="clickValue"
    class="absolute"
    :style="containerStyle"
  >
    <div ref="motionRef" class="text-left">
      <slot />
    </div>
  </div>
</template>
