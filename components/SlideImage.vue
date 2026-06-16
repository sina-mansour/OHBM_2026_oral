<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'
import { useNav } from '@slidev/client'
import { useMotion } from '@vueuse/motion'

type MotionState = Record<string, any>
type CropDef = { x0?: number; x1?: number; y0?: number; y1?: number }

const props = defineProps<{
  src: string
  cx?: number    // center-x % of slide (default 50)
  cy?: number    // center-y % of slide (default 50)
  w?: number     // width % of slide    (default 30)
  h?: number     // height % of slide   (default 25)
  x0?: number    // left crop fraction  (default 0)
  x1?: number    // right crop fraction (default 1)
  y0?: number    // top crop fraction   (default 0)
  y1?: number    // bottom crop fraction(default 1)
  initial?: MotionState
  enter?: MotionState
  leave?: MotionState
  clickStates?: Record<number, MotionState>
  cropStates?: Record<number, CropDef>
  containerStates?: Record<number, { cx?: number; cy?: number; w?: number; h?: number }>
}>()

const { clicks } = useNav()
const rootRef = ref<HTMLElement | null>(null)
const containerRef = ref<HTMLElement | null>(null)
const imgRef = ref<HTMLImageElement | null>(null)
const isVisible = ref(false)

// ── Natural image dimensions (set on load) ────────────────────────────────────
const naturalW = ref(0)
const naturalH = ref(0)

function onImgLoad() {
  if (!imgRef.value) return
  naturalW.value = imgRef.value.naturalWidth
  naturalH.value = imgRef.value.naturalHeight
}

// ── Container pixel dimensions (set by ResizeObserver) ───────────────────────
const containerW = ref(0)
const containerH = ref(0)

let resizeObs: ResizeObserver | null = null

// ── Default motion states ─────────────────────────────────────────────────────
const DEFAULT_INITIAL: MotionState = { x: -50, opacity: 0 }
const effectiveInitial = computed<MotionState>(() => props.initial ?? DEFAULT_INITIAL)
const effectiveEnter = computed<MotionState>(() =>
  props.enter ?? { x: 0, y: 0, scale: 1, opacity: 1, transition: { type: 'tween', duration: 500 } }
)
const effectiveLeave = computed<MotionState>(() =>
  props.leave ?? { x: 50, opacity: 0, transition: { type: 'tween', duration: 400 } }
)

// ── Container motion ──────────────────────────────────────────────────────────
const { apply: containerApply } = useMotion(containerRef, { initial: effectiveInitial.value })

// ── Container visibility (v-click via MutationObserver) ───────────────────────
let observer: MutationObserver | null = null
onMounted(() => {
  if (!rootRef.value) return
  isVisible.value = !rootRef.value.classList.contains('slidev-vclick-hidden')
  observer = new MutationObserver(() => {
    isVisible.value = !rootRef.value!.classList.contains('slidev-vclick-hidden')
  })
  observer.observe(rootRef.value, { attributes: true, attributeFilter: ['class'] })
  if (isVisible.value) containerApply({ ...effectiveEnter.value, transition: { duration: 0 } })
  else containerApply({ ...effectiveInitial.value, transition: { duration: 0 } })

  resizeObs = new ResizeObserver(([e]) => {
    containerW.value = e.contentRect.width
    containerH.value = e.contentRect.height
  })
  if (containerRef.value) resizeObs.observe(containerRef.value)
})
onUnmounted(() => {
  observer?.disconnect()
  resizeObs?.disconnect()
})

watch(isVisible, (v) => {
  if (v) {
    containerApply(effectiveEnter.value)
  } else {
    containerApply(effectiveLeave.value)
    setTimeout(() => {
      if (!isVisible.value) containerApply({ ...effectiveInitial.value, transition: { duration: 0 } })
    }, (effectiveLeave.value.transition?.duration ?? 400) + 50)
  }
})

// ── clickStates (container position/scale overrides) ─────────────────────────
watch(clicks, (n) => {
  if (!isVisible.value) return
  if (props.clickStates) {
    const sorted = Object.entries(props.clickStates)
      .map(([k, v]) => [Number(k), v] as [number, MotionState])
      .sort((a, b) => a[0] - b[0])
    let state: MotionState | null = null
    for (const [threshold, s] of sorted)
      if (n >= threshold) state = s
    containerApply(state ?? effectiveEnter.value)
  }
})

// ── Active crop helper ────────────────────────────────────────────────────────
function activeCrop(): Required<CropDef> {
  const crop: Required<CropDef> = {
    x0: props.x0 ?? 0,
    x1: props.x1 ?? 1,
    y0: props.y0 ?? 0,
    y1: props.y1 ?? 1,
  }
  if (props.cropStates) {
    const n = clicks.value
    const sorted = Object.entries(props.cropStates)
      .map(([k, v]) => [Number(k), v] as [number, CropDef])
      .sort((a, b) => a[0] - b[0])
    for (const [t, s] of sorted) if (n >= t) Object.assign(crop, s)
  }
  return crop
}

// ── Container positioning ─────────────────────────────────────────────────────
const containerDims = computed(() => {
  const base = { cx: props.cx ?? 50, cy: props.cy ?? 50, w: props.w ?? 30, h: props.h ?? 25 }
  if (!props.containerStates) return base
  const n = clicks.value
  const sorted = Object.entries(props.containerStates)
    .map(([k, v]) => [Number(k), v] as [number, typeof base])
    .sort((a, b) => a[0] - b[0])
  const result = { ...base }
  for (const [t, s] of sorted)
    if (n >= t) Object.assign(result, s)
  return result
})

const containerStyle = computed(() => {
  const { cx, cy, w, h } = containerDims.value
  return {
    position: 'absolute' as const,
    left:   `${cx - w / 2}%`,
    top:    `${cy - h / 2}%`,
    width:  `${w}%`,
    height: `${h}%`,
    transition: 'left 0.5s ease, top 0.5s ease, width 0.5s ease, height 0.5s ease',
  }
})

// ── Image crop via object-fit/object-position inside overflow:hidden container ─
const imgStyle = computed(() => {
  // Hide until all dimensions are loaded — avoids % → px transition bug
  if (!naturalW.value || !naturalH.value || !containerW.value || !containerH.value) {
    return {
      position: 'absolute' as const,
      left: '0px', top: '0px',
      width: '0px', height: '0px',
      opacity: '0',
      transition: 'none',
      display: 'block',
    }
  }

  const { x0, x1, y0, y1 } = activeCrop()
  const fw = x1 - x0   // fraction of natural width to show
  const fh = y1 - y0   // fraction of natural height to show

  // Size the img so the crop region exactly fills the container
  // (same as cover semantics applied to the crop window)
  const imgW = containerW.value / fw
  const imgH = containerH.value / fh
  const scale = Math.max(imgW / naturalW.value, imgH / naturalH.value)

  // Pixel object-position: shift the scaled natural image so that
  // the crop origin (x0, y0) aligns with the img element's top-left.
  // The container then clips the img to containerW × containerH,
  // revealing exactly the crop region.
  const ox = -(x0 * naturalW.value * scale)
  const oy = -(y0 * naturalH.value * scale)

  return {
    position: 'absolute' as const,
    left: '0px',
    top: '0px',
    width: `${Math.round(imgW)}px`,
    height: `${Math.round(imgH)}px`,
    objectFit: 'cover' as const,
    objectPosition: `${ox.toFixed(1)}px ${oy.toFixed(1)}px`,
    transition: 'width 0.5s ease, height 0.5s ease, object-position 0.5s ease',
    display: 'block',
    opacity: '1',
  }
})
</script>

<template>
  <div ref="rootRef" class="absolute inset-0 pointer-events-none">
    <div ref="containerRef" class="overflow-hidden relative" :style="containerStyle">
      <img ref="imgRef" :src="src" :style="imgStyle" @load="onImgLoad" />
    </div>
  </div>
</template>
