<script setup>
import { ref, computed, onMounted } from 'vue'

const props = defineProps({ notizen: Array })
const emit = defineEmits(['notiz-gespeichert'])

const titel = ref('')
const aktiveMarkierung = ref('')
const editorRef = ref(null)

const markierfarben = [
  { name: 'blau',  hex: '#63b3ed', symbol: '🔵' },
  { name: 'rot',   hex: '#fc8181', symbol: '🔴' },
  { name: 'gruen', hex: '#68d391', symbol: '🟢' },
  { name: 'gelb',  hex: '#f6e05e', symbol: '🟡' }
]

const textfarben = [
  { name: 'weiss',   hex: '#e8f4fd' },
  { name: 'gelb',    hex: '#f6e05e' },
  { name: 'hellblau', hex: '#90cdf4' },
  { name: 'gruen',   hex: '#68d391' },
  { name: 'rot',     hex: '#fc8181' },
]

const zeichenAnzahl = computed(() => {
  return editorRef.value ? editorRef.value.innerText.length : 0
})

onMounted(() => {
  const gespeichert = localStorage.getItem('vue-diary-notizen')
  if (gespeichert) {
    emit('notiz-gespeichert', JSON.parse(gespeichert))
  }
})

// Formatierung — Fett, Kursiv, Unterstrichen
const formatieren = (befehl) => {
  document.execCommand(befehl, false, null)
  editorRef.value.focus()
}

// Textfarbe ändern
const textFarbeSetzen = (hex) => {
  document.execCommand('foreColor', false, hex)
  editorRef.value.focus()
}

// Markieren wie Textmarker
const markierenMitFarbe = (hex) => {
  document.execCommand('hiliteColor', false, hex)
  editorRef.value.focus()
}

// Speichern
const speichern = () => {
  const inhalt = editorRef.value ? editorRef.value.innerHTML : ''
  if (editorRef.value.innerText.trim() === '') return
  const neueNotizen = [...props.notizen, {
    id: Date.now(),
    titel: titel.value || 'Ohne Titel',
    inhalt: inhalt,
    markierung: aktiveMarkierung.value,
    datum: new Date().toLocaleDateString('de-DE')
  }]
  localStorage.setItem('vue-diary-notizen', JSON.stringify(neueNotizen))
  emit('notiz-gespeichert', neueNotizen)
  titel.value = ''
  editorRef.value.innerHTML = ''
  aktiveMarkierung.value = ''
}

// Neues Blatt
const neuesBlatt = () => {
  titel.value = ''
  editorRef.value.innerHTML = ''
  aktiveMarkierung.value = ''
}
</script>

<template>
  <div class="diary-editor">

    <input
      v-model="titel"
      placeholder="Titel deiner Notiz..."
      class="titel-feld"
    />

    <!-- Toolbar -->
    <div class="rich-toolbar">

      <!-- Formatierung -->
      <div class="toolbar-gruppe">
        <button class="toolbar-btn" @click="formatieren('bold')"><b>B</b></button>
        <button class="toolbar-btn" @click="formatieren('italic')"><i>I</i></button>
        <button class="toolbar-btn" @click="formatieren('underline')"><u>U</u></button>
      </div>

      <!-- Textfarben -->
      <div class="toolbar-gruppe">
        <button
          v-for="farbe in textfarben"
          :key="farbe.name"
          class="farb-btn"
          :style="{ background: farbe.hex }"
          @click="textFarbeSetzen(farbe.hex)"
          :title="farbe.name"
        ></button>
      </div>

      <!-- Markierfarben -->
      <div class="toolbar-gruppe">
        <button
          v-for="farbe in markierfarben"
          :key="farbe.name"
          class="markier-btn"
          :style="{ background: farbe.hex }"
          :class="{ aktiv: aktiveMarkierung === farbe.hex }"
          @click="markierenMitFarbe(farbe.hex)"
        >{{ farbe.symbol }}</button>
      </div>

    </div>

    <!-- Textfeld contenteditable -->
    <div
      ref="editorRef"
      contenteditable="true"
      class="grosses-textfeld"
      data-placeholder="Schreibe deine Gedanken hier..."
    ></div>

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