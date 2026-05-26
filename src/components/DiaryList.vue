<script setup>
import { ref } from 'vue'
import { Swiper, SwiperSlide } from 'swiper/vue'
import { Navigation, Pagination } from 'swiper/modules'
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'

defineProps({ notizen: Array })
const emit = defineEmits(['notiz-loeschen', 'notiz-bearbeiten'])

const lightboxBild = ref(null)
</script>

<template>
  <div class="diary-list">

    <h2>Meine Notizen 📋</h2>

    <div v-if="notizen.length === 0" class="leer-hinweis">
      <p>Noch keine Notizen — schreibe deine erste! 💙</p>
    </div>

    <Swiper
      v-else
      :modules="[Navigation, Pagination]"
      :slides-per-view="1.1"
      :centered-slides="true"
      :space-between="16"
      navigation
      :pagination="{ clickable: true }"
      class="notizen-slider"
    >
      <SwiperSlide
        v-for="notiz in notizen"
        :key="notiz.id"
      >
        <div
          class="notiz-karte"
          :style="notiz.markierung ? { borderLeft: `4px solid ${notiz.markierung}` } : {}"
        >
          <div class="notiz-header">
            <h3>{{ notiz.titel }}</h3>
            <div class="notiz-actions">
              <span class="notiz-datum">{{ notiz.datum }}</span>
              <button class="bearbeiten-btn" @click="emit('notiz-bearbeiten', notiz)">✏️</button>
              <button class="loeschen-btn" @click="emit('notiz-loeschen', notiz.id)">🗑️</button>
            </div>
          </div>

          <p v-html="notiz.inhalt"></p>

          <!-- PDF Anhang -->
          <div v-if="notiz.pdfName" class="pdf-anhang">
            <a :href="notiz.pdfData" :download="notiz.pdfName" class="pdf-link">
              📄 {{ notiz.pdfName }} herunterladen
            </a>
          </div>

          <!-- Bilder -->
          <div v-if="notiz.bilder && notiz.bilder.length > 0" class="bild-galerie">
            <div
              v-for="(bild, index) in notiz.bilder"
              :key="index"
              class="bild-wrapper"
              @click="lightboxBild = bild"
            >
              <img :src="bild" class="vorschau-bild" />
              <div class="bild-overlay">🔍</div>
            </div>
          </div>

        </div>
      </SwiperSlide>
    </Swiper>

    <!-- Lightbox -->
    <div v-if="lightboxBild" class="lightbox" @click="lightboxBild = null">
      <div class="lightbox-inhalt" @click.stop>
        <img :src="lightboxBild" class="lightbox-bild" />
        <button class="lightbox-schliessen" @click="lightboxBild = null">✕</button>
      </div>
    </div>

  </div>
</template>

<style scoped>
</style>