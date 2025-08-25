<script setup>
import { ref, onMounted } from "vue"

const articles = ref([])
const loading = ref(true)
const progress = ref(0) // pour la progress bar

onMounted(async () => {
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=6")
    const data = await res.json()

    // Simuler le chargement avec progress bar
    let pct = 0
    const interval = setInterval(() => {
      pct += 5
      progress.value = pct
      if (pct >= 100) {
        clearInterval(interval)
        articles.value = data
        loading.value = false
      }
    }, 75) // durée totale ~1.5s
    
  } catch (e) {
    console.error("Erreur API :", e)
    loading.value = false
  }
})
</script>

<template>
  <div class="container">
    <h1>Liste des articles</h1>

    <!-- Progress bar -->
    <div v-if="loading" class="progress-container">
      <div class="progress-bar" :style="{ width: progress + '%' }"></div>
    </div>

    <!-- Liste des articles avec transition -->
    <transition-group name="fade" tag="div" class="articles" v-else>
      <div v-for="article in articles" :key="article.id" class="article">
        <h2>{{ article.title }}</h2>
        <p>{{ article.body }}</p>
      </div>
    </transition-group>
  </div>
</template>
