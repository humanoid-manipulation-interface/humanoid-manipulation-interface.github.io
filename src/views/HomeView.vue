<script setup lang="ts">
import { ref, onMounted } from 'vue'
import arxivIconUrl from '@/assets/arxiv.svg'
import githubIconUrl from '@/assets/github.svg'
import pdfIconUrl from '@/assets/PDF.svg'
import CapabilitiesSection from '@/components/CapabilitiesSection.vue'
import GeneralizationSection from '@/components/GeneralizationSection.vue'
import DataCollectionSection from '@/components/DataCollectionSection.vue'

const videoSrc = `${import.meta.env.BASE_URL}teaser.mp4`
const teaserImgSrc = `${import.meta.env.BASE_URL}teaser.png`

const activeSection = ref('hero')
const isNavVisible = ref(false)

onMounted(() => {
  // 1. Navigation Visibility using IntersectionObserver on a sentinel
  const triggerEl = document.getElementById('nav-trigger')
  if (triggerEl) {
    const navObserver = new IntersectionObserver((entries) => {
      // If trigger is NOT intersecting (scrolled past it), nav is visible
      if (entries[0]) {
        isNavVisible.value = !entries[0].isIntersecting
      }
    }, {
      threshold: 0
    })
    navObserver.observe(triggerEl)
  }

  // 2. Active Section Highlighting
  const sectionObserver = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        activeSection.value = entry.target.id
      }
    })
  }, {
    rootMargin: '-45% 0px -45% 0px'
  })

  const sections = ['hero', 'capabilities', 'generalization', 'data-collection', 'abstract']
  sections.forEach(id => {
    const el = document.getElementById(id)
    if (el) sectionObserver.observe(el)
  })
})


type Author = {
  key: string
  name: string
  starred?: boolean
  href?: string
}

const authors: Author[] = [
  { key: 'ruiqian-nai', name: 'Ruiqian Nai', starred: true, href: 'https://ruiqiannai.github.io/' },
  { key: 'boyuan-zheng', name: 'Boyuan Zheng', starred: true, href: 'https://scholar.google.com/citations?user=ifGjX54AAAAJ' },
  { key: 'junming-zhao', name: 'Junming Zhao', starred: true, href: 'https://junmingzhao20.github.io/' },
  { key: 'haodong-zhu', name: 'Haodong Zhu' },
  { key: 'sicong-dai', name: 'Sicong Dai' },
  { key: 'zunhao-chen', name: 'Zunhao Chen' },
  { key: 'yihang-hu', name: 'Yihang Hu' },
  { key: 'yingdong-hu', name: 'Yingdong Hu', href: 'https://yingdong-hu.github.io/' },
  { key: 'tong-zhang', name: 'Tong Zhang', href: 'https://tongzhangthu.github.io/' },
  { key: 'chuan-wen', name: 'Chuan Wen', href: 'https://alvinwen428.github.io/' },
  { key: 'yang-gao', name: 'Yang Gao', href: 'https://yanggao.weebly.com/' },
]

type HeroButton = {
  key: string
  label: string
  href?: string
  disabled?: boolean
}

const heroButtons: HeroButton[] = [
  { key: 'arxiv', label: 'Arxiv', href: 'http://arxiv.org/abs/2602.06643' },
  { key: 'paper', label: 'Paper', href: `${import.meta.env.BASE_URL}humi.pdf` },
  { key: 'code', label: 'Code (Coming Soon)', disabled: true },
]

const scrollTo = (id: string) => {
  const element = document.getElementById(id)
  if (element) {
    element.scrollIntoView({ behavior: 'smooth' })
  }
}
</script>

<template>
  <div class="scrollRoot">
    <!-- Sentinel for navigation visibility -->
    <div id="nav-trigger" style="position: absolute; top: 80vh; left: 0; width: 1px; height: 1px; pointer-events: none;"></div>

    <!-- Floating Navigation -->
    <transition name="fade">
      <div class="floating-nav" v-show="isNavVisible">
        <div 
          class="nav-item" 
          :class="{ active: activeSection === 'capabilities' }"
          @click="scrollTo('capabilities')"
        >
          <span class="nav-text">Capabilities</span>
          <div class="nav-dot"></div>
        </div>
        <div 
          class="nav-item" 
          :class="{ active: activeSection === 'generalization' }"
          @click="scrollTo('generalization')"
        >
          <span class="nav-text">Generalization</span>
          <div class="nav-dot"></div>
        </div>
        <div 
          class="nav-item" 
          :class="{ active: activeSection === 'data-collection' }"
          @click="scrollTo('data-collection')"
        >
          <span class="nav-text">Data Collection</span>
          <div class="nav-dot"></div>
        </div>
        <div 
          class="nav-item" 
          :class="{ active: activeSection === 'abstract' }"
          @click="scrollTo('abstract')"
        >
          <span class="nav-text">Abstract</span>
          <div class="nav-dot"></div>
        </div>
      </div>
    </transition>

    <v-sheet id="hero" class="panel hero" rounded="0">
      <video
        class="heroVideo"
        :src="videoSrc"
        autoplay
        muted
        loop
        playsinline
        preload="metadata"
        aria-hidden="true"
      ></video>

      <div class="heroOverlay" aria-hidden="true"></div>

      <v-container fluid class="heroContent fill-height d-flex align-center justify-center text-center">
        <div class="heroText">
          <div class="heroHeadline font-weight-bold">
            HuMI
          </div>
          <div class="heroSubtitle font-weight-regular">
            Humanoid Whole-Body Manipulation<br />
            from Robot-Free Demonstrations
          </div>
          <div class="heroAuthors">
            <template v-for="(author, index) in authors" :key="author.key">
              <component :is="author.href ? 'a' : 'span'" :href="author.href" class="heroAuthor">
                <span class="heroAuthorName">{{ author.name }}</span>
                <span v-if="author.starred" class="heroAuthorStar">*</span>
              </component>
              <span v-if="index < authors.length - 1">, </span>
              <br v-if="author.key === 'sicong-dai'" />
            </template>
          </div>
          <div class="heroFootnote">
            <span class="heroFootnoteStar">*</span>
            <span>Equal Contribution</span>
          </div>
          <div class="heroButtons">
            <v-btn
              v-for="button in heroButtons"
              :key="button.key"
              :href="button.href"
              :disabled="button.disabled || !button.href"
              color="primary"
              size="large"
              variant="elevated"
              rounded="pill"
              class="heroButton"
            >
              <img v-if="button.key === 'arxiv'" :src="arxivIconUrl" class="heroButtonIcon" alt="" aria-hidden="true" />
              <img
                v-else-if="button.key === 'paper'"
                :src="pdfIconUrl"
                class="heroButtonIcon"
                alt=""
                aria-hidden="true"
              />
              <img v-else-if="button.key === 'code'" :src="githubIconUrl" class="heroButtonIcon" alt="" aria-hidden="true" />
              {{ button.label }}
            </v-btn>
          </div>
        </div>
      </v-container>
    </v-sheet>

    <CapabilitiesSection id="capabilities"></CapabilitiesSection>

    <GeneralizationSection id="generalization"></GeneralizationSection>

    <DataCollectionSection id="data-collection"></DataCollectionSection>

    <v-sheet id="abstract" class="panel abstract" rounded="0" aria-label="Abstract section">
      <v-container class="abstractInner">
        <div class="abstractTitle font-weight-bold">Abstract</div>
        <div class="mt-4 text-body-1 abstractText" lang="en">
          Current approaches for humanoid whole-body manipulation, primarily relying on teleoperation or visual
          sim-to-real reinforcement learning, are hindered by hardware logistics and complex reward engineering.
          Consequently, demonstrated autonomous skills remain limited and are typically restricted to controlled
          environments. In this paper, we present the <strong>Hu</strong>manoid <strong>M</strong>anipulation
          <strong>I</strong>nterface (<strong>HuMI</strong>), a portable and efficient framework for learning diverse
          whole-body manipulation tasks across various environments. HuMI enables
          robot-free data collection by capturing rich whole-body motion using portable hardware. This data drives a
          hierarchical learning pipeline that translates human motions into dexterous and feasible humanoid skills.
          Extensive experiments across five whole-body tasks—including kneeling, squatting, tossing, walking, and
          bimanual manipulation—demonstrate that HuMI achieves a 3x increase in data collection efficiency compared to
          teleoperation and attains a 70% success rate in unseen environments.
        </div>
      </v-container>
    </v-sheet>

    <v-sheet class="panel teaser-image-section pb-16" rounded="0">
      <v-container class="d-flex justify-center pt-0">
        <v-img
          :src="teaserImgSrc"
          alt="HuMI Teaser"
          elevation="0"
          class="rounded-lg teaser-img"
          eager
          cover
        ></v-img>
      </v-container>
    </v-sheet>
  </div>
</template>

<style scoped>
.teaser-image-section {
  background-color: var(--color-background);
}

.teaser-img {
  width: min(65vw, 82ch) !important;
  /* Ensure resizing behaves */
  flex: 0 0 auto;
}

@media (max-width: 600px) {
  .teaser-img {
    width: 95vw !important;
  }
}

.scrollRoot {
  width: 100%;
  overflow-x: hidden;
}

.panel {
  width: 100%;
}

.hero {
  position: relative;
  overflow: hidden;
  height: 100vh;
}

.heroVideo {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.heroOverlay {
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.45);
}

.heroContent {
  position: relative;
  padding: 4rem 1.5rem;
}

.heroText {
  width: 90vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.35rem;
}

.heroHeadline {
  max-width: 100%;
  color: #ffffff;
  font-size: clamp(5.5rem, 13vw, 11rem);
  line-height: 1;
  letter-spacing: -0.02em;
  text-shadow: 0 10px 30px rgba(0, 0, 0, 0.45);
}

.heroSubtitle {
  max-width: 100%;
  margin: 0;
  color: #ffffff;
  font-size: clamp(1.5rem, 2.8vw, 2.6rem) !important;
  line-height: 1.25;
}

.heroAuthors {
  width: 70%;
  margin-top: 1.25rem;
  color: rgba(255, 255, 255, 0.9);
  font-size: clamp(1rem, 1.6vw, 1.35rem);
  line-height: 1.35;
}

.heroAuthor {
  display: inline-block;
  white-space: nowrap;
  color: inherit;
  text-decoration: none;
  padding: 0 !important;
  background-color: transparent !important;
}

.heroAuthor:hover,
.heroAuthor:focus-visible {
  background-color: transparent !important;
}

.heroAuthor:hover .heroAuthorName,
.heroAuthor:focus-visible .heroAuthorName {
  text-decoration: underline;
}

.heroAuthorName {
  display: inline-block;
  padding: 0 0.2em;
  border-radius: 0.25em;
  text-decoration-skip-ink: none;
}

.heroAuthor:hover .heroAuthorName,
.heroAuthor:focus-visible .heroAuthorName {
  background-color: rgba(120, 190, 255, 0.3);
}

.heroAuthorStar {
  text-decoration: none;
}

.heroFootnote {
  margin-top: 0.25rem;
  color: rgba(255, 255, 255, 0.9);
  font-size: clamp(0.85rem, 1.2vw, 1rem);
  line-height: 1.3;
}

.heroButtons {
  margin-top: 2rem;
  display: flex;
  flex-wrap: wrap;
  gap: 1.25rem;
  justify-content: center;
}

.heroButton {
  text-transform: none;
  letter-spacing: normal;
}

.heroButtonIcon {
  display: inline-block;
  width: 1.1em;
  height: 1.1em;
  margin-right: 0.55rem;
  object-fit: contain;
  /* Ensure icon color matches text */
  filter: brightness(0);
  transition: filter 0.2s ease, opacity 0.2s ease;
}

.heroButton.v-btn:not(.v-btn--disabled) {
  background-color: rgba(255, 255, 255, 0.9) !important;
  color: rgba(0, 0, 0, 0.7) !important;
}

.heroButton.v-btn:not(.v-btn--disabled) .heroButtonIcon {
  opacity: 0.7;
}

.heroButton.v-btn:not(.v-btn--disabled):hover {
  background-color: #1f6fff !important;
  color: #ffffff !important;
}

.heroButton.v-btn:not(.v-btn--disabled):hover .heroButtonIcon {
  filter: brightness(0) invert(1);
  opacity: 1;
}

.heroButton.v-btn.v-btn--disabled {
  opacity: 0.8 !important;
  background-color: rgba(255, 255, 255, 0.7) !important;
  color: rgba(0, 0, 0, 0.4) !important;
  cursor: not-allowed !important;
  pointer-events: auto !important;
}

.heroButton.v-btn.v-btn--disabled :deep(*) {
  cursor: not-allowed !important;
}

.heroButton.v-btn.v-btn--disabled .heroButtonIcon {
  opacity: 0.4;
}

.abstract {
  background: var(--color-background);
  color: var(--color-text);
}

.abstractInner {
  padding-top: 1rem;
  padding-bottom: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.abstractTitle {
  text-align: center;
  width: 100%;
  font-size: clamp(1.35rem, 1.8vw, 1.75rem);
  color: var(--color-heading);
}

.abstractText {
  width: min(60vw, 75ch);
  text-align: left;
  text-align: justify;
  text-align-last: left;
  text-justify: inter-word;
  hyphens: none;
  word-spacing: 0.03em;
  font-size: clamp(1.05rem, 1.25vw, 1.25rem);
  line-height: 1.7;
  opacity: 0.92;
  color: var(--color-text);
}

@media (max-width: 600px) {
  .abstractText {
    width: 90vw;
  }
  .floating-nav {
    display: none !important;
  }
}

.floating-nav {
  position: fixed;
  right: 24px;
  top: 40px;
  z-index: 100;
  display: flex;
  flex-direction: column;
  gap: 10px;
  /* Removed background/box styles for a cleaner look */
}

.floating-nav:hover {
  /* No background change on hover */
}

.nav-item {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 12px;
  cursor: pointer;
  padding: 8px 4px;
}

.nav-text {
  font-size: 0.8rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: #333;
  opacity: 0;
  transform: translateX(10px);
  transition: all 0.3s ease;
  pointer-events: none; /* Prevent accidental text hovering */
  text-shadow: 0 0 4px #fff;
}

.nav-item:hover .nav-text {
  opacity: 1;
  transform: translateX(0);
}

.nav-dot {
  width: 8px;
  height: 8px;
  background-color: rgba(0, 0, 0, 0.4);
  border-radius: 50%;
  transition: all 0.3s ease;
  border: 1px solid rgba(255, 255, 255, 0.5);
}

.nav-item:hover .nav-dot,
.nav-item.active .nav-dot {
  background-color: #000;
  transform: scale(1.4);
  border-color: transparent;
}

@media (prefers-color-scheme: dark) {
  .nav-text {
    color: var(--vt-c-text-dark-1);
    text-shadow: 0 0 4px var(--color-background);
  }
  
  .nav-dot {
    background-color: rgba(255, 255, 255, 0.4);
    border: 1px solid rgba(0, 0, 0, 0.5);
  }
  
  .nav-item:hover .nav-dot,
  .nav-item.active .nav-dot {
    background-color: #fff;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
