<script setup lang="ts">
const getUrl = (file: string) => {
  return `${import.meta.env.BASE_URL}data_collection/${file}`
}
</script>

<template>
  <v-sheet class="panel data-collection bg-grey-lighten-4" rounded="0">
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

            <!-- TWIST2 Card -->
             <v-col cols="12" md="6" class="d-flex flex-column align-center px-4 mb-8">
                <v-card class="w-100 rounded-lg overflow-hidden d-flex flex-column" elevation="3">
                    <div class="video-container bg-black position-relative">
                        <div class="video-label bg-grey-darken-3 text-white px-3 py-1 font-weight-bold" style="position: absolute; top: 10px; left: 10px; z-index: 2; border-radius: 4px;">TWIST2</div>
                        <video
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
</style>
