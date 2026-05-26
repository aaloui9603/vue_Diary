<script setup>
defineProps({ notizen: Array })
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
    >
      <div class="notiz-header">
        <h3>{{ notiz.titel }}</h3>
        <span class="notiz-datum">{{ notiz.datum }}</span>
      </div>
      <p>{{ notiz.inhalt }}</p>
    </div>

  </div>
</template>

<style scoped>
</style>