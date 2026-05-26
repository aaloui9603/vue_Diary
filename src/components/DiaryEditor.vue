<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const props = defineProps({ 
  notizen: Array,
  zuBearbeiten: Object
})
const emit = defineEmits(['notiz-gespeichert', 'bearbeitung-fertig'])

const titel = ref('')
const aktiveMarkierung = ref('')
const editorRef = ref(null)
const bilder = ref([])
const pdfName = ref('')
const pdfData = ref(null)

const markierfarben = [
  { name: 'blau',  hex: '#63b3ed', symbol: '🔵' },
  { name: 'rot',   hex: '#fc8181', symbol: '🔴' },
  { name: 'gruen', hex: '#68d391', symbol: '🟢' },
  { name: 'gelb',  hex: '#f6e05e', symbol: '🟡' }
]

const textfarben = [
  { name: 'weiss',    hex: '#e8f4fd' },
  { name: 'gelb',     hex: '#f6e05e' },
  { name: 'hellblau', hex: '#90cdf4' },
  { name: 'gruen',    hex: '#68d391' },
  { name: 'rot',      hex: '#fc8181' },
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

watch(() => props.zuBearbeiten, (notiz) => {
  if (notiz && editorRef.value) {
    titel.value = notiz.titel
    editorRef.value.innerHTML = notiz.inhalt
    aktiveMarkierung.value = notiz.markierung || ''
    bilder.value = notiz.bilder || []
    pdfName.value = notiz.pdfName || ''
    pdfData.value = notiz.pdfData || null
    editorRef.value.focus()
  }
})

const formatieren = (befehl) => {
  document.execCommand(befehl, false, null)
  editorRef.value.focus()
}

const textFarbeSetzen = (hex) => {
  document.execCommand('foreColor', false, hex)
  editorRef.value.focus()
}

const markierenMitFarbe = (hex) => {
  document.execCommand('hiliteColor', false, hex)
  editorRef.value.focus()
}

const bildEinfuegen = (event) => {
  const dateien = Array.from(event.target.files)
  if (bilder.value.length + dateien.length > 5) {
    alert('Maximal 5 Bilder pro Eintrag! 💙')
    return
  }
  dateien.forEach(datei => {
    const reader = new FileReader()
    reader.onload = (e) => {
      bilder.value.push(e.target.result)
    }
    reader.readAsDataURL(datei)
  })
}

const bildEntfernen = (index) => {
  bilder.value.splice(index, 1)
}

const pdfEinfuegen = (event) => {
  const datei = event.target.files[0]
  if (!datei) return
  pdfName.value = datei.name

  const reader = new FileReader()
  reader.onload = (e) => {
    pdfData.value = e.target.result
  }
  reader.readAsDataURL(datei)
}

const pdfEntfernen = () => {
  pdfName.value = ''
  pdfData.value = null
}

const speichern = () => {
  const inhalt = editorRef.value ? editorRef.value.innerHTML : ''
  if (editorRef.value.innerText.trim() === '') return
  const neueNotizen = [...props.notizen, {
    id: Date.now(),
    titel: titel.value || 'Ohne Titel',
    inhalt: inhalt,
    markierung: aktiveMarkierung.value,
    bilder: bilder.value,
    pdfName: pdfName.value,
    pdfData: pdfData.value,
    datum: new Date().toLocaleDateString('de-DE')
  }]
  localStorage.setItem('vue-diary-notizen', JSON.stringify(neueNotizen))
  emit('notiz-gespeichert', neueNotizen)
  emit('bearbeitung-fertig')
  titel.value = ''
  editorRef.value.innerHTML = ''
  aktiveMarkierung.value = ''
  bilder.value = []
  pdfName.value = ''
  pdfData.value = null
}

const neuesBlatt = () => {
  titel.value = ''
  editorRef.value.innerHTML = ''
  aktiveMarkierung.value = ''
  bilder.value = []
  pdfName.value = ''
  pdfData.value = null
  emit('bearbeitung-fertig')
}
</script>

<template>
  <div class="diary-editor">

    <input
      v-model="titel"
      placeholder="Titel deiner Notiz..."
      class="titel-feld"
    />

    <div class="rich-toolbar">
      <div class="toolbar-gruppe">
        <button class="toolbar-btn" @click="formatieren('bold')"><b>B</b></button>
        <button class="toolbar-btn" @click="formatieren('italic')"><i>I</i></button>
        <button class="toolbar-btn" @click="formatieren('underline')"><u>U</u></button>
      </div>

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

    <!-- Bilder einfügen -->
    <div class="upload-leiste">
      <label class="bild-upload-btn">
        📷 Bild
        <input
          type="file"
          accept="image/*"
          multiple
          @change="bildEinfuegen"
          style="display: none"
        />
      </label>
      <span class="bild-zaehler">{{ bilder.length }}/5</span>

      <!-- PDF einfügen -->
      <label class="pdf-upload-btn">
        📄 PDF
        <input
          type="file"
          accept="application/pdf"
          @change="pdfEinfuegen"
          style="display: none"
        />
      </label>

      <div v-if="pdfName" class="pdf-anzeige">
        <span>📎 {{ pdfName }}</span>
        <button class="pdf-loeschen" @click="pdfEntfernen">✕</button>
      </div>
    </div>

    <!-- Bild Vorschau -->
    <div v-if="bilder.length > 0" class="bild-galerie">
      <div
        v-for="(bild, index) in bilder"
        :key="index"
        class="bild-wrapper"
      >
        <img :src="bild" class="vorschau-bild" />
        <button class="bild-loeschen" @click="bildEntfernen(index)">✕</button>
      </div>
    </div>

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