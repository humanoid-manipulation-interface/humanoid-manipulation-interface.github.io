<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'

const getUrl = (file: string) => {
  return `${import.meta.env.BASE_URL}data_collection/${file}`
}

const getPublicAssetUrl = (file: string) => {
  return `${import.meta.env.BASE_URL}${file}`
}

const rootElement = ref<HTMLElement | null>(null)
const videoRefs = ref<(HTMLVideoElement | null)[]>([])
const bagStep = ref(0) // 0: Closed, 1: Open, 2: Reveal Video
let observer: IntersectionObserver | null = null

const setVideoRef = (el: any, index: number) => {
  if (el) videoRefs.value[index] = el as HTMLVideoElement
}

const handleBagClick = () => {
  if (bagStep.value > 0) return
  bagStep.value = 1
  setTimeout(() => {
    bagStep.value = 2
  }, 1300) // Reduced delay
}

const onVideoEnded = () => {
  bagStep.value = 0
}

watch(bagStep, (newStep) => {
  if (newStep === 2) {
    const video = videoRefs.value[2]
    if (video) {
      video.currentTime = 0
      video.play().catch(() => {})
    }
  }
})

onMounted(() => {
  if (rootElement.value) {
    observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          videoRefs.value.forEach(v => v?.play().catch(() => {}))
        } else {
          videoRefs.value.forEach(v => v?.pause())
        }
      })
    }, {
      threshold: 0.3 // Play when 30% visible
    })
    observer.observe(rootElement.value)
  }
})

onUnmounted(() => {
  if (observer) observer.disconnect()
})
</script>

<template>
  <v-sheet class="panel data-collection" rounded="0">
    <div ref="rootElement" class="w-100 h-100">
      <v-container class="fill-height pb-16" style="max-width: 1200px; padding-top: clamp(2rem, 5vw, 3.5rem);">
        <v-row class="w-100 flex-column align-center" justify="center">
          <v-col cols="12" :class="['text-center', bagStep === 2 ? 'mb-4 mb-md-8' : 'mb-1 mb-md-2']">
             <div class="sectionTitle font-weight-bold">Portable Data Collection</div>
          </v-col>        
          
          <!-- Backpack Section (Animated) -->
          <v-row justify="center" :class="['w-100 position-relative', bagStep === 2 ? 'mb-8 mb-md-6' : 'mb-0']">
            <v-col cols="12" md="10" class="position-relative backpack-wrapper px-4" :class="bagStep === 2 ? 'video-active' : ''">
              
              <!-- Video Layer (Revealed at Step 2) -->
              <div 
                  class="position-absolute h-100 d-flex align-center justify-center"
                  :style="{ 
                      opacity: bagStep === 2 ? 1 : 0, 
                      transform: bagStep === 2 ? 'scale(1)' : 'scale(0.95)',
                      transition: 'all 1s ease-in-out',
                      zIndex: 0,
                      pointerEvents: bagStep === 2 ? 'auto' : 'none',
                      top: 0, left: '16px', right: '16px',
                      width: 'auto'
                  }"
              >
                   <div class="rounded-lg overflow-hidden elevation-2 bg-black" style="width: 100%; max-width: 700px;">
                      <video
                          :ref="(el) => setVideoRef(el, 2)"
                          :src="getUrl('data-collection.mp4')"
                          controls
                          autoplay
                          muted
                          playsinline
                          @ended="onVideoEnded"
                          class="w-100 d-block"
                      ></video>
                   </div>
              </div>

              <!-- Interactive Layer (Text + Bag) -->
              <div 
                  class="d-flex align-center justify-center w-100 h-100 position-absolute"
                  :style="{ 
                      pointerEvents: bagStep === 2 ? 'none' : 'auto',
                      cursor: bagStep === 2 ? 'default' : 'pointer',
                      zIndex: 1,
                      top: 0, left: 0
                  }"
                  @click="handleBagClick"
              >
                  <!-- Text -->
                  <div 
                      class="text-center"
                      :style="{ 
                          opacity: bagStep === 2 ? 0 : 1, 
                          transform: bagStep === 2 ? 'translateX(-30px)' : 'none',
                          transition: 'all 0.8s ease',
                          maxWidth: '240px',
                          marginRight: '-20px',
                          cursor: bagStep === 2 ? 'default' : 'pointer'
                      }"
                  >
                      <div class="text-h6 font-weight-bold mb-4 backpack-text" style="line-height: 1.2;">Our entire setup fits into a single backpack!</div>
                      <div class="text-subtitle-1 text-primary d-flex align-center justify-center font-weight-bold">
                         Click to see inside
                         <v-icon icon="mdi-cursor-default-click" size="default" class="ml-1"></v-icon>
                      </div>
                  </div>

                  <!-- Bag Images -->
                  <div class="position-relative d-flex align-center justify-center backpack-image-container">
                      <!-- Closed Bag -->
                      <v-img 
                          :src="getPublicAssetUrl('bag-closed.png')"
                          :style="{ 
                              opacity: bagStep === 0 ? 1 : 0,
                              transform: bagStep === 0 ? 'scale(1)' : 'scale(0.95)',
                              transition: 'all 0.4s ease-out',
                              position: 'absolute'
                          }"
                          width="100%"
                          contain
                          class="bag-img"
                      ></v-img>

                      <!-- Open Bag -->
                      <v-img 
                          :src="getPublicAssetUrl('bag-open.png')"
                          :style="{ 
                              opacity: bagStep === 2 ? 0 : (bagStep >= 1 ? 1 : 0),
                              transform: bagStep === 2 ? 'translateX(100px)' : (bagStep >= 1 ? 'scale(1)' : 'scale(0.8)'),
                              transition: bagStep === 2 ? 'all 0.8s ease' : 'transform 0.6s cubic-bezier(0.34, 1.56, 0.64, 1), opacity 0.4s ease',
                              position: 'absolute'
                          }"
                          width="100%"
                          contain
                          class="bag-img"
                      ></v-img>
                  </div>
              </div>
            </v-col>
          </v-row>

          <v-row class="w-100" justify="center" :style="{ marginTop: bagStep === 2 ? '0' : '0', transition: 'margin-top 1s ease' }">
              <!-- HuMI Card -->
              <v-col cols="12" md="6" class="d-flex flex-column align-center px-4 mb-8">
                  <v-card class="w-100 rounded-lg overflow-hidden d-flex flex-column" elevation="3">
                      <div class="video-container bg-black position-relative">
                           <div class="video-label bg-primary text-white px-3 py-1 font-weight-bold" style="position: absolute; top: 10px; left: 10px; z-index: 2; border-radius: 4px;">Ours (HuMI)</div>
                          <video
                              :ref="(el) => setVideoRef(el, 0)"
                              :src="getUrl('humi.mp4')"
                              controls
                              muted
                              loop
                              playsinline
                              class="w-100 d-block"
                              style="aspect-ratio: 16/9; object-fit: cover;"
                          ></video>
                      </div>
                      <div class="pa-6 text-center">
                          <div class="text-h2 font-weight-bold text-primary mb-1">60</div>
                          <div class="text-subtitle-1 font-weight-medium">Valid Demos</div>
                          <div class="text-caption text-grey-darken-1">in 15 minutes</div>
                      </div>
                  </v-card>
              </v-col>

              <!-- Teleoperation Card -->
              <v-col cols="12" md="6" class="d-flex flex-column align-center px-4 mb-8">
                  <v-card class="w-100 rounded-lg overflow-hidden d-flex flex-column" elevation="3">
                      <div class="video-container bg-black position-relative">
                          <div class="video-label bg-grey-darken-3 text-white px-3 py-1 font-weight-bold" style="position: absolute; top: 10px; left: 10px; z-index: 2; border-radius: 4px;">Teleoperation</div>
                          <video
                              :ref="(el) => setVideoRef(el, 1)"
                              :src="getUrl('twist2.mp4')"
                              controls
                              muted
                              loop
                              playsinline
                              class="w-100 d-block"
                              style="aspect-ratio: 16/9; object-fit: cover;"
                          ></video>
                      </div>
                      <div class="pa-6 text-center">
                          <div class="text-h2 font-weight-bold text-grey-darken-1 mb-1">18</div>
                          <div class="text-subtitle-1 font-weight-medium">Valid Demos</div>
                           <div class="text-caption text-grey-darken-1">in 15 minutes</div>
                      </div>
                  </v-card>
              </v-col>          
          </v-row>
        </v-row>
      </v-container>
    </div>
  </v-sheet>
</template>

<style scoped>
.panel {
  width: 100%;
  background-color: var(--color-background);
}

.sectionTitle {
  text-align: center;
  width: 100%;
  font-size: clamp(1.35rem, 1.8vw, 1.75rem);
  color: var(--color-heading);
}

.backpack-text {
  color: var(--color-heading);
}

.backpack-wrapper {
  min-height: 200px;
  transition: min-height 1s ease;
}

.backpack-wrapper.video-active {
  min-height: 280px;
}

.backpack-image-container {
  width: 200px;
  height: 200px;
}

.bag-img {
  height: 200px;
}

@media (min-width: 960px) {
  .backpack-wrapper {
    min-height: 270px;
  }
  .backpack-wrapper.video-active {
    min-height: 400px;
  }
  .backpack-image-container {
    width: 300px;
    height: 270px;
  }
  .bag-img {
    height: 270px;
  }
}

@media (prefers-color-scheme: dark) {
  .v-card {
    background-color: var(--vt-c-black-soft) !important;
    color: var(--color-text) !important;
  }
}
</style>