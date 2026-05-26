<script setup>
import { ref } from 'vue'
const istGestartet = ref(false)
</script>

<template>
  <div class="app-container">
    <div v-if="!istGestartet" class="landing-card">
      <div class="pearl-ring"></div>
      <h1>📔 Vue-Diary</h1>
      <p>Mein persönliches &amp; digitales Tagebuch <br>schreibe, markiere und speichere deine Gedanken</p>
      <button @click="istGestartet = true">Tagebuch öffnen ✨</button>
    </div>
    <div v-else>
      <h2>Willkommen in deinem Tagebuch 💙</h2>
    </div>
  </div>

</template>

<style scoped></style>
