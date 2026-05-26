<script setup>
import { ref } from 'vue'
import DiaryEditor from './components/DiaryEditor.vue'
import DiaryList from './components/DiaryList.vue'

const istGestartet = ref(false)
const notizen = ref([])
const zuBearbeitendeNotiz = ref(null)

const notizLoeschen = (id) => {
  notizen.value = notizen.value.filter(n => n.id !== id)
  localStorage.setItem('vue-diary-notizen', JSON.stringify(notizen.value))
}

const notizBearbeiten = (notiz) => {
  zuBearbeitendeNotiz.value = notiz
  notizen.value = notizen.value.filter(n => n.id !== notiz.id)
  localStorage.setItem('vue-diary-notizen', JSON.stringify(notizen.value))
}
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
      <DiaryEditor
        :notizen="notizen"
        :zuBearbeiten="zuBearbeitendeNotiz"
        @notiz-gespeichert="notizen = $event"
        @bearbeitung-fertig="zuBearbeitendeNotiz = null"
      />
      <DiaryList
        :notizen="[...notizen].sort((a, b) => b.id - a.id)"
        @notiz-loeschen="notizLoeschen"
        @notiz-bearbeiten="notizBearbeiten"
      />
    </div>

  </div>
</template>

<style scoped></style>