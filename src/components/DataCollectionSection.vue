<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const getUrl = (file: string) => {
  return `${import.meta.env.BASE_URL}data_collection/${file}`
}

const rootElement = ref<HTMLElement | null>(null)
const videoRefs = ref<(HTMLVideoElement | null)[]>([])
let observer: IntersectionObserver | null = null

const setVideoRef = (el: any, index: number) => {
  if (el) videoRefs.value[index] = el as HTMLVideoElement
}

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
      <v-container class="fill-height pb-16" style="max-width: 1200px; padding-top: clamp(1rem, 3vw, 2.5rem);">
        <v-row class="w-100 flex-column align-center" justify="center">
          <v-col cols="12" class="text-center mb-8">
             <div class="sectionTitle font-weight-bold">Portable Data Collection</div>
          </v-col>

          <v-row class="w-100" justify="center">
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

@media (prefers-color-scheme: dark) {
  .v-card {
    background-color: var(--vt-c-black-soft) !important;
    color: var(--color-text) !important;
  }
}
</style>
