<script setup lang="ts">
import { ref } from 'vue'
import logoUrl from '../assets/LOGO.png'
</script>

<template>
    <div class="abs-bl m-6 flex items-center gap-2">
        <img :src="logoUrl" alt="OHBM 2026" class="h-4 w-auto opacity-90">
    </div>
    <div class="abs-br m-6 flex items-center gap-2">
        <div class="text-3 text-left text-slate-400"> <carbon:chat /> {{ $slidev.configs.title }} </div>
    </div>
    <div class="abs-tr mt-8 mr-6 flex items-center gap-2 bg-slate-100 rounded-lg shadow-lg border-1 pl-1 pr-1">
        <div class="text-3 text-left text-slate-400"> <carbon:presentation-file /> <SlideCurrentNo class="text-primary" /> / <SlidesTotal class="text-slate-500" /> </div>
    </div>
    <div class="absolute bottom-3.4 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 0.15);"></div>
    <div class="absolute bottom-1.7 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 0.5);"></div>
    <div class="absolute bottom-0 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 1.0);"></div>
    <div class="absolute top-0 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 1.0);"></div>
    <div class="absolute top-1.7 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 0.5);"></div>
    <div class="absolute top-3.4 left-0 right-0 flex items-center gap-2 h-1.7 w-full" style="background: rgba(26, 179, 204, 0.15);"></div>
</template>
