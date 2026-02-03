<script setup lang="ts">
import { ref } from 'vue'

type Video = {
  label: string
  file: string
}

type Capability = {
  title: string
  folder: string
  videos: Video[]
}

const capabilities: Capability[] = [
  {
    title: 'Walking to Clean a Table 🧹',
    folder: 'clean',
    videos: [
      { label: 'Video 1', file: '1.mp4' },
      { label: 'Video 2', file: '2.mp4' },
      { label: 'Video 3', file: '3.mp4' }
    ]
  },
  {
    title: 'Marriage Proposal 💍',
    folder: 'proposal',
    videos: [
      { label: 'Video 1', file: '1.mp4' },
      { label: 'Video 2', file: '2.mp4' }
    ]
  },
  {
    title: 'Tossing a Toy 🧸',
    folder: 'toss',
    videos: [
      { label: 'Left', file: 'left1.mp4' },
      { label: 'Right', file: 'right1.mp4' }
    ]
  },
  {
    title: 'Unsheathing a Sword 🗡️',
    folder: 'unsheathe',
    videos: [
      { label: 'Video 1', file: '1.mp4' },
      { label: 'Video 2', file: '2.mp4' }
    ]
  }
]

const selections = ref<Video[]>(capabilities.map(cap => cap.videos[0]) as Video[])

const getUrl = (folder: string, file: string) => {
  return `${import.meta.env.BASE_URL}capabilities/${folder}/${file}`
}

const getSelectedFile = (index: number) => {
  return selections.value[index]?.file || ''
}

const nextVideo = (index: number) => {
  const currentVideo = selections.value[index]
  const cap = capabilities[index]
  
  if (!currentVideo || !cap) return

  const currentIndex = cap.videos.findIndex(v => v.file === currentVideo.file)
  if (currentIndex === -1) return

  const nextIndex = (currentIndex + 1) % cap.videos.length
  selections.value[index] = cap.videos[nextIndex]
}
</script>

<template>
  <v-sheet class="panel capabilities bg-grey-lighten-4" rounded="0">
    <v-container class="fill-height pb-8" style="max-width: 1200px; padding-top: clamp(2rem, 5vw, 4rem);">
      <v-row class="w-100" justify="center">
        <v-col cols="12" class="text-center mb-2">
           <div class="sectionTitle font-weight-bold">Whole-Body Capabilities</div>
        </v-col>

        <v-col
          v-for="(cap, index) in capabilities"
          :key="cap.title"
          cols="12"
          sm="6"
          class="d-flex flex-column align-center px-4 mb-8"
        >
          <v-card class="capability-card w-100 rounded-lg overflow-hidden d-flex flex-column" elevation="3">
            <!-- Video Area -->
            <div class="video-container bg-black">
              <video
                :key="getSelectedFile(index)"
                :src="getUrl(cap.folder, getSelectedFile(index))"
                controls
                autoplay
                muted
                loop
                playsinline
                class="w-100 d-block"
                style="aspect-ratio: 16/9; object-fit: cover;"
              ></video>
            </div>

            <!-- Content Area -->
            <div class="pa-4 d-flex align-center justify-space-between">
              <div class="text-subtitle-1 font-weight-medium text-left">{{ cap.title }}</div>
              
              <v-btn
                v-if="cap.videos.length > 1"
                variant="text"
                color="primary"
                size="small"
                @click="nextVideo(index)"
                append-icon="mdi-skip-next"
              >
                Next Episode
              </v-btn>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-sheet>
</template>

<style scoped>
.panel {
  width: 100%;
  /* Ensure background matches or contrasts nicely */
  background-color: rgb(var(--v-theme-surface));
}

.sectionTitle {
  text-align: center;
  width: 100%;
  font-size: clamp(1.35rem, 1.8vw, 1.75rem);
}

.capabilities {
  /* Optional: Add some padding if content overflows on small screens */
  overflow-y: auto;
}

/* Adjustments for responsiveness if needed */
@media (max-width: 960px) {
  .panel {
    height: auto; /* Allow growth on mobile */
    min-height: 100vh;
  }
}
</style>
