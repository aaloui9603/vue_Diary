<script setup>
import { ref } from 'vue'
import DiaryEditor from './components/DiaryEditor.vue'
import DiaryList from './components/DiaryList.vue'

const istGestartet = ref(false)
const notizen = ref([])
</script>

<template>
  <div class="app-container">

    <div v-if="!istGestartet" class="landing-card">
      <div class="pearl-ring"></div>
      <h1>📔 Vue-Diary</h1>
      <p>Mein persönliches & digitales Tagebuch<br>schreibe, markiere und speichere deine Gedanken</p>
      <button @click="istGestartet = true">Tagebuch öffnen ✨</button>
    </div>

    <div v-else>
      <DiaryEditor :notizen="notizen" @notiz-gespeichert="notizen = $event" />
      <DiaryList :notizen="notizen" />
    </div>

  </div>
</template>

<style scoped></style>