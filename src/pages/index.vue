<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useElementBounding, useEventListener, useWindowScroll } from '@vueuse/core'
import { ChevronsUpDown, DollarSign, Key, CreditCard, BarChart, MapPin, Mail } from '@lucide/vue'

const slider = ref<HTMLElement | null>(null)
const isDragging = ref(false)
const position = ref(40)
const showTooltip = ref(false)

const { left, width } = useElementBounding(slider)

// Tooltip interaction
onMounted(() => {
  setTimeout(() => {
    showTooltip.value = true
    setTimeout(() => {
      showTooltip.value = false
    }, 3500)
  }, 1200)
})

const updatePosition = (clientX: number) => {
  if (width.value === 0) return
  let pct = ((clientX - left.value) / width.value) * 100
  pct = Math.max(0, Math.min(pct, 100))
  position.value = pct
  
  if (showTooltip.value) {
    showTooltip.value = false
  }
}

const handleStart = (e: MouseEvent | TouchEvent) => {
  isDragging.value = true
  const clientX = 'touches' in e ? e.touches[0].clientX : e.clientX
  updatePosition(clientX)
}

const handleMove = (e: MouseEvent | TouchEvent) => {
  if (!isDragging.value) return
  const clientX = 'touches' in e ? e.touches[0].clientX : e.clientX
  updatePosition(clientX)
}

const handleEnd = () => {
  isDragging.value = false
}

// Listen to move and end on window
useEventListener(window, 'mousemove', handleMove)
useEventListener(window, 'mouseup', handleEnd)
useEventListener(window, 'touchmove', handleMove, { passive: false })
useEventListener(window, 'touchend', handleEnd)
</script>

<template>
  <div class="font-body-md text-on-surface bg-black min-h-screen">
    <!-- TopNavBar -->
    <nav id="main-nav" class="fixed top-0 w-full z-50 flex justify-between items-center px-margin-desktop transition-all duration-300" :class="{ 'bg-black/90 backdrop-blur-sm py-sm': scrollY > 50, 'bg-transparent py-md': scrollY <= 50 }">
      <div class="flex-1">
        <div class="font-headline-md text-headline-md font-extrabold text-on-surface tracking-tighter">CLADALE</div>
      </div>
      <div class="hidden md:flex flex-1 justify-center items-center space-x-lg">
        <a class="nav-link font-semibold" href="#">Home</a>
        <a class="nav-link" href="#">Services</a>
        <a class="nav-link" href="#">Projects</a>
        <a class="nav-link" href="#">About</a>
        <a class="nav-link" href="#">Contact</a>
      </div>
      <div class="flex-1 flex justify-end items-center space-x-md">
        <a class="nav-link hidden lg:block" href="#">Login</a>
        <button class="bg-primary-container text-on-primary-container px-md py-xs rounded-full font-label-caps text-[10px] tracking-widest hover:brightness-110 transition-all duration-300">
          REQUEST QUOTE
        </button>
      </div>
    </nav>

    <!-- Hero Section -->
    <main class="relative min-h-screen pt-[120px] pb-xl flex flex-col items-center justify-start overflow-hidden px-margin-desktop text-center">
      <!-- Interactive Comparison Slider - Top of Content -->
      <div class="w-full max-w-5xl relative z-20 mb-xl">
        <div class="relative w-full group">
          <!-- Tooltip -->
          <div 
            class="absolute top-0 left-1/2 -translate-x-1/2 -translate-y-[120%] z-40 bg-primary-container text-white px-md py-xs font-label-caps uppercase text-[10px] tracking-widest transition-opacity duration-700 pointer-events-none"
            :class="showTooltip ? 'opacity-100' : 'opacity-0'"
            id="slider-tooltip"
          >
            Drag to compare
          </div>
          <div 
            ref="slider"
            class="comparison-container relative aspect-[1.79/1] w-full overflow-hidden" 
            id="comparison-slider"
          >
            <!-- After Image (Base) -->
            <img 
              alt="Completed building" 
              class="absolute inset-0 w-full h-full object-contain" 
              src="/hero_building_(2).png"
            />
            <!-- Before Image (Clipped) -->
            <div 
              class="absolute inset-0 z-10 overflow-hidden" 
              :style="{ width: `${position}%` }"
              id="before-image-wrapper"
            >
              <img 
                alt="Under construction" 
                class="absolute inset-0 w-[100vw] max-w-none h-full object-contain" 
                :style="{ width: `${width}px` }"
                src="/hero_building_(1).png"
              />
            </div>
            <!-- Slider Handle: Red Line -->
            <div 
              class="slider-handle absolute top-0 bottom-0 w-[2px] bg-primary-container z-40 flex items-center justify-center" 
              :style="{ left: `${position}%` }"
              id="slider-drag"
              @mousedown="handleStart"
              @touchstart.prevent="handleStart"
            >
              <!-- Drag Grip -->
              <div class="absolute w-12 h-12 bg-primary-container rounded-full border-[6px] border-surface shadow-2xl flex items-center justify-center group-hover:scale-110 transition-transform duration-300">
                <ChevronsUpDown class="text-white w-5 h-5" />
              </div>
            </div>
            <!-- Labels -->
            <div class="absolute bottom-md left-md z-30 font-label-caps text-[10px] text-white/40 tracking-[0.2em] pointer-events-none uppercase">Foundation</div>
            <div class="absolute bottom-md right-md z-30 font-label-caps text-[10px] text-white/40 tracking-[0.2em] pointer-events-none uppercase">Completion</div>
          </div>
        </div>
      </div>
      <!-- Hero Content -->
      <div class="max-w-4xl z-10">
        <h2 class="font-headline-lg text-white mb-sm uppercase leading-tight tracking-tighter font-black">
          FROM FOUNDATION<br/>
          <span class="text-primary-container">TO COMPLETION</span>
        </h2>
        <h5 class="font-headline-md text-primary-container mb-lg tracking-[0.1em] font-bold uppercase">
          Experience the Cladale Difference
        </h5>
        <div class="flex flex-col sm:flex-row items-center justify-center gap-md mb-xl">
          <button class="bg-primary-container text-on-primary-container px-xl py-md rounded-full text-body-md font-bold uppercase tracking-widest hover:brightness-110 transition-all duration-300 w-full sm:w-auto">
            Request Quote
          </button>
          <button class="border border-on-surface text-on-surface px-xl py-md rounded-full text-body-md font-bold uppercase tracking-widest hover:bg-on-surface hover:text-surface transition-all duration-300 w-full sm:w-auto">
            Start Here
          </button>
        </div>
      </div>
    </main>

    <!-- Footer -->
    <footer class="w-full flex flex-col md:flex-row justify-between items-center px-margin-desktop py-xl bg-surface-container-lowest border-t border-surface-container-high/30 z-30 relative">
      <div class="mb-md md:mb-0">
        <div class="font-headline-md text-headline-md font-black text-on-surface">CLADALE</div>
      </div>
      <div class="flex flex-wrap justify-center gap-md mb-md md:mb-0">
        <a class="font-data-mono text-data-mono text-on-secondary-container hover:text-primary-container transition-colors uppercase text-[11px]" href="#">Privacy Policy</a>
        <a class="font-data-mono text-data-mono text-on-secondary-container hover:text-primary-container transition-colors uppercase text-[11px]" href="#">Terms of Service</a>
        <a class="font-data-mono text-data-mono text-on-secondary-container hover:text-primary-container transition-colors uppercase text-[11px]" href="#">Safety Standards</a>
        <a class="font-data-mono text-data-mono text-on-secondary-container hover:text-primary-container transition-colors uppercase text-[11px]" href="#">Careers</a>
      </div>
      <div class="font-data-mono text-[10px] text-on-secondary-container/60 tracking-wider">
        © 2024 CLADALE INDUSTRIAL. ALL RIGHTS RESERVED.
      </div>
    </footer>
  </div>
</template>
