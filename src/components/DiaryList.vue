<script setup>
defineProps({ notizen: Array })
const emit = defineEmits(['notiz-loeschen'])
</script>

<template>
  <div class="diary-list">

    <h2>Meine Notizen 📋</h2>

    <div v-if="notizen.length === 0" class="leer-hinweis">
      <p>Noch keine Notizen — schreibe deine erste! 💙</p>
    </div>

    <div
      v-for="notiz in notizen"
      :key="notiz.id"
      class="notiz-karte"
      :style="notiz.markierung ? { borderLeft: `4px solid ${notiz.markierung}` } : {}"
      >
  
      <div class="notiz-header">
        <h3>{{ notiz.titel }}</h3>
        <div class="notiz-actions">
          <span class="notiz-datum">{{ notiz.datum }}</span>
          <button
            class="loeschen-btn"
            @click="emit('notiz-loeschen', notiz.id)"
          >🗑️</button>
        </div>
      </div>
      <p v-html="notiz.inhalt"></p>
    </div>

  </div>
</template>

<style scoped>
</style>