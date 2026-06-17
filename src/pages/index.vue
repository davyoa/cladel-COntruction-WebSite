<script setup lang="ts">
import { ref, computed, onUnmounted } from 'vue'
import { useWindowScroll } from '@vueuse/core'
import {
  Tag,
  KeyRound,
  CreditCard,
  BarChart3,
  ArrowLeft,
  ArrowRight,
  ArrowRight as ArrowForward,
  MapPin,
  Mail,
} from '@lucide/vue'

// Nav scroll behavior
const { y: scrollY } = useWindowScroll()

const hero = ref<HTMLElement | null>(null)
const currentX = ref(100)
const currentY = ref(50)
const targetX = ref(100)
const targetY = ref(50)
const animationFrame = ref<number | null>(null)

const clamp = (value: number, min: number, max: number) => Math.min(max, Math.max(min, value))

const animateScene = () => {
  currentX.value += (targetX.value - currentX.value) * 0.08
  currentY.value += (targetY.value - currentY.value) * 0.08

  if (Math.abs(targetX.value - currentX.value) > 0.15 || Math.abs(targetY.value - currentY.value) > 0.15) {
    animationFrame.value = window.requestAnimationFrame(animateScene)
  } else {
    currentX.value = targetX.value
    currentY.value = targetY.value
    animationFrame.value = null
  }
}

const setPointer = (clientX: number, clientY: number) => {
  if (!hero.value) return
  const rect = hero.value.getBoundingClientRect()
  targetX.value = clamp(((clientX - rect.left) / rect.width) * 100, 0, 100)
  targetY.value = clamp(((clientY - rect.top) / rect.height) * 100, 0, 100)
  if (animationFrame.value === null) {
    animationFrame.value = window.requestAnimationFrame(animateScene)
  }
}

const handlePointerMove = (event: MouseEvent) => {
  setPointer(event.clientX, event.clientY)
}

const handlePointerLeave = () => {
  targetX.value = 100
  targetY.value = 50
  if (animationFrame.value === null) {
    animationFrame.value = window.requestAnimationFrame(animateScene)
  }
}

const sceneStyle = computed(() => ({
  transform: `perspective(1400px) rotateY(${(currentX.value - 50) * 0.11}deg) rotateX(${(50 - currentY.value) * 0.07}deg)`,
}))

const beforeMaskStyle = computed(() => ({
  clipPath: `polygon(0 0, ${currentX.value}% 0, ${currentX.value}% 100%, 0 100%)`,
}))

const revealStyle = computed(() => ({
  left: `${currentX.value}%`,
  transform: 'translateX(-50%)',
}))

onUnmounted(() => {
  if (animationFrame.value !== null) {
    window.cancelAnimationFrame(animationFrame.value)
  }
})
</script>

<template>
  <div class="font-body-md text-on-surface bg-black min-h-screen">
    <!-- NAVIGATION SHELL -->
    <nav
      id="main-nav"
      class="fixed top-0 w-full z-50 flex justify-between items-center px-margin-desktop border-b border-white/5 transition-all duration-300 ease-in-out"
      :class="scrollY > 50 ? 'bg-black/90 backdrop-blur-sm py-sm' : 'bg-transparent py-md'"
    >
      <div class="flex items-center gap-base">
        <span class="font-headline-md text-headline-md font-black text-on-surface tracking-tighter">CLADALE</span>
      </div>
      <div class="hidden md:flex gap-lg items-center">
        <a class="nav-link text-primary-container font-bold border-b-2 border-primary-container pb-1" href="#">Home</a>
        <a class="nav-link text-on-surface" href="#services">Services</a>
        <a class="nav-link text-on-surface" href="#offers">Projects</a>
        <a class="nav-link text-on-surface" href="#about">About</a>
        <a class="nav-link text-on-surface" href="#contact">Contact</a>
      </div>
      <button class="bg-primary-container text-white px-md py-xs font-label-caps uppercase hover:bg-white hover:text-primary-container transition-all duration-300">
        Request Quote
      </button>
    </nav>

    <main>
      <!-- HERO SECTION -->
      <section
        ref="hero"
        @mousemove="handlePointerMove"
        @mouseleave="handlePointerLeave"
        class="relative min-h-[110vh] pt-[90px] pb-lg flex flex-col items-center justify-start overflow-hidden px-margin-desktop text-center bg-black"
      >
        <!-- Hero Content -->
        <div class="max-w-5xl z-10 fade-in-on-scroll" style="animation-delay: 0.2s">
          <h2 class="font-headline-xl text-[clamp(2.75rem,6vw,4.5rem)] text-white mb-md uppercase leading-tight tracking-tighter animate-fade-in-up">
            Your building and construction experts
          </h2>
          <h5 class="font-headline-lg text-primary-container mb-xl tracking-[0.18em] uppercase animate-fade-in-up" style="animation-delay: 0.1s">
            in Ghana
          </h5>
        </div>

        <div class="w-full max-w-6xl relative z-20 mb-lg fade-in-on-scroll">
          <div class="relative w-full group overflow-hidden rounded-[32px] shadow-[0_24px_120px_rgba(0,0,0,0.45)]">
            <div
              class="relative overflow-hidden rounded-[32px] bg-black/60 aspect-[1.9/1]"
              :style="sceneStyle"
            >
              <img
                alt="Completed building"
                class="absolute inset-0 w-full h-full object-scale-down"
                src="/hero_building_(2).png"
              />

              <div
                class="absolute inset-0 overflow-hidden"
                :style="beforeMaskStyle"
              >
                <img
                  alt="Unfinished building"
                  class="absolute inset-0 w-full h-full object-scale-down"
                  src="/hero_building_(1).png"
                />
              </div>

              <div
                class="absolute top-0 left-0 right-0 bottom-0 pointer-events-none"
                :style="revealStyle"
              >
                <div class="absolute top-0 left-1/2 h-full w-[2px] -translate-x-1/2 bg-red-500/70 shadow-[0_0_24px_rgba(214,40,40,0.6)] blur-sm" />
                <div class="absolute top-1/2 left-1/2 h-5 w-5 -translate-x-1/2 -translate-y-1/2 rounded-full bg-red-500/55 shadow-[0_0_20px_rgba(214,40,40,0.55)]" />
              </div>
            </div>
          </div>
        </div>

        <div class="w-full max-w-4xl mx-auto mb-10 px-4 py-3 rounded-full bg-surface-container/80 border border-white/10 text-white/80 text-sm tracking-[0.2em] uppercase shadow-lg backdrop-blur-xl">
          <span class="font-label-caps text-primary-container mr-2">Location: </span>
          AK-846-2803, Kumasi, Ghana 00233
        </div>
      </section>

      <!-- ABOUT SECTION -->
      <section class="py-lg px-margin-desktop border-t border-white/10 bg-black fade-in-on-scroll" id="about">
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-gutter items-start">
          <div class="lg:col-span-4">
            <h2 class="font-label-caps text-primary-container mb-sm animate-slide-in-left">CLADALE OVERVIEW</h2>
            <h3 class="font-headline-lg text-white uppercase mb-md animate-slide-in-left" style="animation-delay: 0.1s">BUILDING GHANA'S FUTURE</h3>
          </div>
          <div class="lg:col-span-8 border-l border-white/10 pl-lg">
            <p class="font-body-lg text-on-secondary-container mb-md leading-relaxed fade-in-on-scroll">
              Cladale Industrial stands at the forefront of the construction sector in Ghana. We bridge the gap between traditional reliability and modern technical mastery. We focus on structural integrity, precision engineering, and the unapologetic pursuit of industrial excellence.
            </p>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-sm mt-lg">
              <div class="bg-surface-container p-md border border-white/5 fade-in-on-scroll hover:border-primary-container transition-all duration-300 cursor-pointer" style="animation-delay: 0s">
                <span class="font-headline-md text-white block">24+</span>
                <span class="font-label-caps text-xs text-secondary">PROJECTS</span>
              </div>
              <div class="bg-surface-container p-md border border-white/5 fade-in-on-scroll hover:border-primary-container transition-all duration-300 cursor-pointer" style="animation-delay: 0.1s">
                <span class="font-headline-md text-white block">100%</span>
                <span class="font-label-caps text-xs text-secondary">SAFETY</span>
              </div>
              <div class="bg-surface-container p-md border border-white/5 fade-in-on-scroll hover:border-primary-container transition-all duration-300 cursor-pointer" style="animation-delay: 0.2s">
                <span class="font-headline-md text-white block">08+</span>
                <span class="font-label-caps text-xs text-secondary">YEARS</span>
              </div>
              <div class="bg-surface-container p-md border border-white/5 fade-in-on-scroll hover:border-primary-container transition-all duration-300 cursor-pointer" style="animation-delay: 0.3s">
                <span class="font-headline-md text-white block">KMS</span>
                <span class="font-label-caps text-xs text-secondary">REGION</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- SERVICES -->
      <section class="py-xl px-margin-desktop bg-surface-dim fade-in-on-scroll" id="services">
        <div class="mb-lg">
          <h2 class="font-label-caps text-primary-container mb-xs animate-slide-in-left">CORE CAPABILITIES</h2>
          <h3 class="font-headline-lg text-white uppercase animate-slide-in-left" style="animation-delay: 0.1s">INDUSTRIAL SERVICES</h3>
        </div>
        <div class="grid grid-cols-1 md:grid-cols-4 gap-gutter">
          <div class="group bg-surface-container border border-white/10 p-lg hover:border-primary-container transition-all duration-300 industrial-shadow fade-in-on-scroll hover:shadow-lg" style="animation-delay: 0s">
            <Tag class="w-9 h-9 text-primary-container mb-md transition-transform group-hover:scale-110" />
            <h4 class="font-headline-md text-white uppercase mb-sm">SELLING</h4>
            <p class="font-body-md text-on-secondary-container">Technical procurement and asset liquidation for heavy-duty construction equipment.</p>
          </div>
          <div class="group bg-surface-container border border-white/10 p-lg hover:border-primary-container transition-all duration-300 industrial-shadow fade-in-on-scroll hover:shadow-lg" style="animation-delay: 0.1s">
            <KeyRound class="w-9 h-9 text-primary-container mb-md transition-transform group-hover:scale-110" />
            <h4 class="font-headline-md text-white uppercase mb-sm">RENTING</h4>
            <p class="font-body-md text-on-secondary-container">Flexible leasing solutions for high-performance machinery across Ghana.</p>
          </div>
          <div class="group bg-surface-container border border-white/10 p-lg hover:border-primary-container transition-all duration-300 industrial-shadow fade-in-on-scroll hover:shadow-lg" style="animation-delay: 0.2s">
            <CreditCard class="w-9 h-9 text-primary-container mb-md transition-transform group-hover:scale-110" />
            <h4 class="font-headline-md text-white uppercase mb-sm">FINANCES</h4>
            <p class="font-body-md text-on-secondary-container">Strategic capital management and project funding for large-scale developments.</p>
          </div>
          <div class="group bg-surface-container border border-white/10 p-lg hover:border-primary-container transition-all duration-300 industrial-shadow fade-in-on-scroll hover:shadow-lg" style="animation-delay: 0.3s">
            <BarChart3 class="w-9 h-9 text-primary-container mb-md transition-transform group-hover:scale-110" />
            <h4 class="font-headline-md text-white uppercase mb-sm">RATING</h4>
            <p class="font-body-md text-on-secondary-container">Rigorous structural assessment and property valuation services.</p>
          </div>
        </div>
      </section>

      <!-- PROJECTS -->
      <section class="py-xl px-margin-desktop bg-black fade-in-on-scroll" id="offers">
        <div class="mb-lg border-b border-white/10 pb-md flex justify-between items-center">
          <h2 class="font-headline-lg text-white uppercase animate-slide-in-left">FEATURED PROJECTS</h2>
          <div class="flex gap-sm">
            <button class="p-sm border border-white/10 hover:bg-white/5 transition-colors hover:scale-110">
              <ArrowLeft class="w-5 h-5 text-on-surface" />
            </button>
            <button class="p-sm border border-white/10 hover:bg-white/5 transition-colors hover:scale-110">
              <ArrowRight class="w-5 h-5 text-on-surface" />
            </button>
          </div>
        </div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-gutter">
          <!-- Project Card 1 -->
          <article class="group bg-surface-container border border-white/5 overflow-hidden fade-in-on-scroll hover:shadow-2xl" style="animation-delay: 0s">
            <div class="aspect-video relative overflow-hidden">
              <img alt="Modern Residential" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" src="https://lh3.googleusercontent.com/aida/AP1WRLsPPg5V49h1rfbslsI6xFUvG816f5zNP0zJfR2xujzoKRO1u0gSQIyrnSOByIspEyVcSD9xuFhUUQTTOVdr_xcmka2Yi8YMh5EFxBXNQVqjYtwsQtchGGCU3PhyAxdrEIl6IlR57gPP_W2SF2uxVdhz4ir7hYUUJh21l1FICcfnQ24Kx0gD8KwXPa_BijcsDSi8kKcMQ5gC-GxeXufBCZUGtT1BnsWlwFgzFI8UiQjwZ_E6YLtLjGgeLdc"/>
              <div class="absolute top-0 right-0 bg-primary-container px-sm py-xs font-label-caps text-[10px] text-white">FOR SALE</div>
            </div>
            <div class="p-md">
              <h4 class="font-headline-md text-white mb-xs uppercase">RESIDENTIAL ALPHA</h4>
              <p class="font-data-mono text-xs text-secondary mb-md">KUMASI, GHANA | 2023</p>
              <a class="flex items-center gap-xs font-label-caps text-primary-container hover:gap-md transition-all hover:text-white" href="#">
                SEE EXPOSÈ <ArrowForward class="w-4 h-4" />
              </a>
            </div>
          </article>
          <!-- Project Card 2 -->
          <article class="group bg-surface-container border border-white/5 overflow-hidden fade-in-on-scroll hover:shadow-2xl" style="animation-delay: 0.1s">
            <div class="aspect-video relative overflow-hidden">
              <img alt="Luxury Estate" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" src="https://lh3.googleusercontent.com/aida/AP1WRLubstAXz8ugVAGDauRrgRWzW4zht5L2cibYsHH57P_KPW9gUYrOvE99R9p9mEIGmNXcGfoXlk3SN0LWbD9xqIB5IkiLk8mfITGSth6bYJU3EGUc4dRIIvnTjBMadGuyU2Kp9jYEN0W9OChBooynn386ye8HMigouUl_SWsb4dJSEJwrclvy3v0e_iZaq0kM0C1VVJr99QPVHK7SHuGuy4XD3Xhn06Yy9fBou3M597fyUBAslY_ErEN9Ot1R"/>
              <div class="absolute top-0 right-0 bg-primary-container px-sm py-xs font-label-caps text-[10px] text-white">LEASED</div>
            </div>
            <div class="p-md">
              <h4 class="font-headline-md text-white mb-xs uppercase">ESTATE DELTA</h4>
              <p class="font-data-mono text-xs text-secondary mb-md">ACCRA, GHANA | 2024</p>
              <a class="flex items-center gap-xs font-label-caps text-primary-container hover:gap-md transition-all hover:text-white" href="#">
                SEE EXPOSÈ <ArrowForward class="w-4 h-4" />
              </a>
            </div>
          </article>
          <!-- Project Card 3 -->
          <article class="group bg-surface-container border border-white/5 overflow-hidden fade-in-on-scroll hover:shadow-2xl" style="animation-delay: 0.2s">
            <div class="aspect-video relative overflow-hidden">
              <img alt="Coastal Villa" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" src="https://lh3.googleusercontent.com/aida/AP1WRLuzbEcJa_qZHQnoUuuVcmZ9UCxxhCwe53y4QG6XNnHePzSsif33KFG9wXIlUvQvVVsh5eeJFGWaK_k4o3zI7_SFOxMil75SjU2JYvVms_SSbQUs9QXqtYVPPSEZwDrQpIVL4Gyh0Jo0usUCaMkBQt75KF9DHZvtA9mt-WhabsV7a2U8M2ne8EbcYzuaMnVyh-Y890LyH5T07OKf2ZWcIrp3Qluvh31UIkKBz9Y_nuAdgWN31JAQ1ANjDFw"/>
              <div class="absolute top-0 right-0 bg-primary-container px-sm py-xs font-label-caps text-[10px] text-white">FOR SALE</div>
            </div>
            <div class="p-md">
              <h4 class="font-headline-md text-white mb-xs uppercase">VILLA SIGMA</h4>
              <p class="font-data-mono text-xs text-secondary mb-md">CAPE COAST | 2023</p>
              <a class="flex items-center gap-xs font-label-caps text-primary-container hover:gap-md transition-all hover:text-white" href="#">
                SEE EXPOSÈ <ArrowForward class="w-4 h-4" />
              </a>
            </div>
          </article>
        </div>
      </section>

      <!-- CONTACT -->
      <section class="py-xl px-margin-desktop bg-surface-dim border-t border-white/5 fade-in-on-scroll" id="contact">
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-xl">
          <div>
            <h2 class="font-label-caps text-primary-container mb-sm animate-slide-in-left">CONTACT</h2>
            <h3 class="font-headline-lg text-white uppercase mb-lg animate-slide-in-left" style="animation-delay: 0.1s">START YOUR PROJECT</h3>
            <div class="space-y-lg">
              <div class="flex gap-md items-center fade-in-on-scroll hover:translate-x-2 transition-transform">
                <div class="w-12 h-12 bg-primary-container flex items-center justify-center">
                  <MapPin class="text-white w-5 h-5" />
                </div>
                <div>
                  <h4 class="font-label-caps text-secondary text-xs">HEADQUARTERS</h4>
                  <p class="text-white">AK-846-2803, Kumasi, Ghana</p>
                </div>
              </div>
              <div class="flex gap-md items-center fade-in-on-scroll hover:translate-x-2 transition-transform">
                <div class="w-12 h-12 bg-primary-container flex items-center justify-center">
                  <Mail class="text-white w-5 h-5" />
                </div>
                <div>
                  <h4 class="font-label-caps text-secondary text-xs">EMAIL</h4>
                  <p class="text-white">info@cladalconstruction.com</p>
                </div>
              </div>
            </div>
          </div>
          <div class="bg-black p-lg border border-white/10 industrial-shadow fade-in-on-scroll hover:shadow-xl transition-all">
            <form class="space-y-md">
              <input class="w-full bg-surface-container border border-white/10 focus:border-primary-container text-white px-md py-sm transition-all focus:shadow-lg" placeholder="NAME" type="text"/>
              <input class="w-full bg-surface-container border border-white/10 focus:border-primary-container text-white px-md py-sm transition-all focus:shadow-lg" placeholder="EMAIL" type="email"/>
              <textarea class="w-full bg-surface-container border border-white/10 focus:border-primary-container text-white px-md py-sm transition-all focus:shadow-lg" placeholder="MESSAGE" rows="4"></textarea>
              <button class="w-full bg-primary-container text-white py-md font-label-caps uppercase tracking-widest hover:bg-white hover:text-primary-container transition-all hover:shadow-lg" type="submit">
                SUBMIT INQUIRY
              </button>
            </form>
          </div>
        </div>
      </section>

      <!-- MAP SIMULATION -->
      <section class="h-64 bg-black grayscale contrast-125 brightness-50 flex items-center justify-center border-y border-white/5 fade-in-on-scroll">
        <div class="border-2 border-primary-container p-lg bg-black/80 backdrop-blur-md hover:scale-105 transition-transform">
          <p class="font-label-caps text-white">CLADALE OPERATIONS AREA: KUMASI</p>
        </div>
      </section>
    </main>

    <!-- FOOTER -->
    <footer class="bg-black text-primary-container font-data-mono text-xs flex flex-col md:flex-row justify-between items-center px-margin-desktop py-xl w-full border-t border-white/5">
      <div>
        <span class="font-headline-md font-black text-on-surface uppercase">CLADALE.</span>
        <p class="mt-xs text-on-secondary-container">© 2024 CLADALE INDUSTRIAL. ALL RIGHTS RESERVED.</p>
      </div>
      <div class="flex gap-lg mt-lg md:mt-0 uppercase tracking-widest text-[10px]">
        <a class="text-on-secondary-container hover:text-primary-container transition-colors" href="#">Privacy</a>
        <a class="text-on-secondary-container hover:text-primary-container transition-colors" href="#">Terms</a>
        <a class="text-on-secondary-container hover:text-primary-container transition-colors" href="#">Safety</a>
      </div>
    </footer>
  </div>
</template>
