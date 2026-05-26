<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const props = defineProps({ notizen: Array })
const emit = defineEmits(['notiz-gespeichert'])

const titel = ref('')
const notizInhalt = ref('')

const zeichenAnzahl = computed(() => notizInhalt.value.length)

onMounted(() => {
  const gespeichert = localStorage.getItem('vue-diary-notizen')
  if (gespeichert) {
    emit('notiz-gespeichert', JSON.parse(gespeichert))
  }
})

const speichern = () => {
  if (notizInhalt.value.trim() === '') return
  const neueNotizen = [...props.notizen, {
    id: Date.now(),
    titel: titel.value || 'Ohne Titel',
    inhalt: notizInhalt.value,
    datum: new Date().toLocaleDateString('de-DE')
  }]
  localStorage.setItem('vue-diary-notizen', JSON.stringify(neueNotizen))
  emit('notiz-gespeichert', neueNotizen)
  titel.value = ''
  notizInhalt.value = ''
}

const neuesBlatt = () => {
  titel.value = ''
  notizInhalt.value = ''
}
</script>

<template>
  <div class="diary-editor">

    <input
      v-model="titel"
      placeholder="Titel deiner Notiz..."
      class="titel-feld"
    />

    <textarea
      v-model="notizInhalt"
      placeholder="Schreibe deine Gedanken hier..."
      class="grosses-textfeld"
    ></textarea>

    <div class="editor-footer">
      <span class="zeichen-zaehler">{{ zeichenAnzahl }} Zeichen</span>
      <div class="toolbar-symbole">
        <span class="symbol-btn" @click="speichern" title="Speichern">💙</span>
        <span class="symbol-btn" @click="neuesBlatt" title="Neues Blatt">📜</span>
      </div>
    </div>

  </div>
</template>

<style scoped>
</style>