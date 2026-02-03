<script setup lang="ts">
import { ref, computed } from 'vue'

type Video = {
  file: string
}

type Environment = {
  name: string
  folder: string
  videos: Video[]
}

const environments: Environment[] = [
  {
    name: 'Conference Room',
    folder: 'conference room',
    videos: [
      { file: '1.mp4' },
      { file: '2.mp4' },
      { file: '3.mp4' },
      { file: '4.mp4' }
    ]
  },
  {
    name: 'Corridor',
    folder: 'corridor',
    videos: [
      { file: '1.mp4' },
      { file: '2.mp4' },
      { file: '3.mp4' },
      { file: '4.mp4' }
    ]
  },
  {
    name: 'Elevator Lobby',
    folder: 'elevator lobby',
    videos: [
      { file: '1.mp4' },
      { file: '2.mp4' },
      { file: '3.mp4' },
      { file: '4.mp4' },
      { file: '5.mp4' }
    ]
  },
  {
    name: 'Lounge Area',
    folder: 'lounge area',
    videos: [
      { file: '1.mp4' }
    ]
  },
  {
    name: 'Laboratory',
    folder: 'laboratory',
    videos: [
      { file: '1.mp4' },
      { file: '2.mp4' },
      { file: '3.mp4' }
    ]
  }
]

const activeEnvIndex = ref(0)
const videoIndices = ref(environments.map(() => 0))

const getUrl = (folder: string, file: string) => {
  return `${import.meta.env.BASE_URL}generalization/${folder}/${file}`
}

const nextVideo = (envIndex: number) => {
  const env = environments[envIndex]
  const currentIndex = videoIndices.value[envIndex]
  
  if (!env || currentIndex === undefined) return

  videoIndices.value[envIndex] = (currentIndex + 1) % env.videos.length
}

const setActiveEnv = (index: number) => {
  activeEnvIndex.value = index
}

// Logic to determine visual position of each card
// We want a circular list effect.
const getCardStyle = (index: number) => {
  const total = environments.length
  // Calculate relative distance in a circular manner
  let diff = (index - activeEnvIndex.value) % total
  if (diff < -2) diff += total
  if (diff > 2) diff -= total
  
  // Correction for the specific case of 5 items to ensure -2, -1, 0, 1, 2 spread
  // Actually, a simpler way for 5 items:
  // 0: Center
  // 1, -1: Immediate neighbors
  // 2, -2: Far neighbors
  
  // We need a stable visual index relative to center (0).
  // Calculate shortest distance on the circle
  let dist = index - activeEnvIndex.value
  
  // Normalize distance to be within -total/2 to total/2
  if (dist > total / 2) dist -= total
  if (dist < -total / 2) dist += total

  const absDist = Math.abs(dist)
  const zIndex = 10 - absDist
  const scale = 1 - absDist * 0.1
  const opacity = 1 - absDist * 0.2
  const xOffset = dist * 30 // percent overlap

  // Visibility check: purely for optimization, though with 5 items we show all
  const visible = true 

  return {
    transform: `translateX(${xOffset}%) scale(${scale})`,
    zIndex: zIndex,
    opacity: opacity,
    filter: absDist === 0 ? 'none' : 'blur(1px) brightness(0.8)',
    cursor: absDist === 0 ? 'default' : 'pointer',
    position: 'absolute' as const,
    left: '0',
    right: '0',
    margin: 'auto',
    width: '75%', // Wider cards
    transition: 'all 0.5s ease'
  }
}

const cycleEnv = (direction: 1 | -1) => {
  let newIndex = activeEnvIndex.value + direction
  if (newIndex < 0) newIndex = environments.length - 1
  if (newIndex >= environments.length) newIndex = 0
  activeEnvIndex.value = newIndex
}
</script>

<template>
  <v-sheet class="panel generalization bg-grey-lighten-4" rounded="0">
    <v-container class="pb-16" style="max-width: 1200px; padding-top: clamp(1rem, 3vw, 2.5rem);">
      <v-row class="w-100 flex-column align-center" justify="center">
        <v-col cols="12" class="text-center mb-4">
           <div class="sectionTitle font-weight-bold">In-the-Wild Generalization</div>
        </v-col>

        <!-- Carousel Area -->
        <div class="carousel-container w-100 position-relative d-flex align-center justify-center">
            
            <!-- Cards -->
            <div
              v-for="(env, index) in environments"
              :key="env.name"
              class="env-card-wrapper"
              :style="getCardStyle(index)"
              @click="dist => (index !== activeEnvIndex ? setActiveEnv(index) : null)"
            >
              <v-card class="rounded-lg overflow-hidden" elevation="10" v-if="true">
                 <div class="bg-black video-wrapper">
                      <video
                        v-if="env.videos[videoIndices[index] || 0]"
                        :src="getUrl(env.folder, env.videos[videoIndices[index] || 0].file)"
                        controls
                      :autoplay="index === activeEnvIndex"
                      muted
                      loop
                      playsinline
                      class="w-100 d-block"
                      style="aspect-ratio: 16/9; object-fit: cover;"
                    ></video>
                 </div>
                 
                 <!-- Content only visible for active card to reduce clutter -->
                 <div class="pa-4 bg-white d-flex align-center justify-space-between">
                    <div class="text-subtitle-1 font-weight-medium text-left text-truncate">{{ env.name }}</div>
                    
                    <v-btn
                        v-if="env.videos.length > 1 && index === activeEnvIndex"
                        variant="text"
                        color="primary"
                        size="small"
                        @click.stop="nextVideo(index)"
                        append-icon="mdi-skip-next"
                    >
                        Switch Object
                    </v-btn>
                 </div>
              </v-card>
            </div>

            <!-- Navigation Buttons (Optional, for clear affordance) -->
            <v-btn
              icon="mdi-chevron-left"
              variant="tonal"
              class="nav-btn prev"
              style="position: absolute; left: 0; top: 50%; transform: translateY(-50%); z-index: 20;"
              @click="cycleEnv(-1)"
            ></v-btn>
             <v-btn
              icon="mdi-chevron-right"
              variant="tonal"
              class="nav-btn next"
              style="position: absolute; right: 0; top: 50%; transform: translateY(-50%); z-index: 20;"
              @click="cycleEnv(1)"
            ></v-btn>

        </div>
      </v-row>
    </v-container>
  </v-sheet>
</template>

<style scoped>
.panel {
  width: 100%;
}

.sectionTitle {
  text-align: center;
  width: 100%;
  font-size: clamp(1.35rem, 1.8vw, 1.75rem);
}

/* Perspective container for 3D feel */
.carousel-container {
  perspective: 1000px;
  height: 550px;
}

@media (max-width: 960px) {
    .carousel-container {
        height: 350px;
    }
}

/* On mobile, stack them simpler or just allow overflow */
@media (max-width: 600px) {
    .carousel-container {
        height: 220px;
    }
}
</style>
