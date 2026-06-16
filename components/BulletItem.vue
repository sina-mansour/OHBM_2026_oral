<script setup lang="ts">
import { useNav } from '@slidev/client'
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps<{
  hideArrowAt?: number
}>()

const { clicks } = useNav()
const liRef = ref<HTMLElement | null>(null)
const liVisible = ref(false)

let observer: MutationObserver | null = null

onMounted(() => {
  if (!liRef.value) return
  liVisible.value = !liRef.value.classList.contains('slidev-vclick-hidden')
  observer = new MutationObserver(() => {
    liVisible.value = !liRef.value!.classList.contains('slidev-vclick-hidden')
  })
  observer.observe(liRef.value, { attributes: true, attributeFilter: ['class'] })
})

onUnmounted(() => observer?.disconnect())

const arrowVisible = computed(() =>
  liVisible.value && (props.hideArrowAt == null || clicks.value < props.hideArrowAt)
)
</script>

<template>
  <li ref="liRef" class="flex gap-4 pb-2" style="margin-left: -0.5rem;">
    <div class="w-4 flex-shrink-0 relative">
      <Transition
        enter-active-class="transition-all duration-500 ease"
        enter-from-class="opacity-0 -translate-x-8"
        leave-active-class="transition-all duration-400 ease"
        leave-to-class="opacity-0 -translate-x-8"
      >
        <div v-if="arrowVisible" class="absolute">
          <BulletArrow />
        </div>
      </Transition>
    </div>
    <Transition
      enter-active-class="transition-all duration-500 ease"
      enter-from-class="opacity-0 -translate-x-6"
      leave-active-class="transition-all duration-400 ease"
      leave-to-class="opacity-0 -translate-x-6"
    >
      <div v-if="liVisible" class="flex-1 min-w-0"><slot /></div>
    </Transition>
  </li>
</template>
