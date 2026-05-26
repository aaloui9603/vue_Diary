<script setup>
import { ref, computed } from 'vue'

const titel = ref('')
const notizInhalt = ref('')

const zeichenAnzahl = computed(() => notizInhalt.value.length)
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
            <span class="zeichen-zaehler">{{  zeichenAnzahl  }} Zeichen</span>
            <div class="toolbar-symbole">
                <span class="symbol-btn" title="Speichern">💙</span>
                <span class="symbol-btn" title="Neues Blatt">📜</span>
            </div>
        </div>
    </div>
</template>

<style scoped>
</style>