<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import { useRoute } from 'vue-router'

const route = useRoute()
</script>
<template>
  <div id="app">
    <header class="site-header">
      <div class="logo">Jainex</div>
      <nav class="main-nav">
        <RouterLink to="/home">Home</RouterLink>
        <RouterLink to="/experience">Experience</RouterLink>
        <RouterLink to="/projects">Projects</RouterLink>
        <RouterLink to="/contact">Contact</RouterLink>
      </nav>
    </header>

    <main class="content">
      <RouterView />
    </main>
  </div>
</template>

<style scoped>
#app {
  margin: 0;
  padding: 0;
  min-height: 100vh;
  color: #ffffff;
  font-family: sans-serif;
}

.site-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 2rem;
  position: sticky;
  top: 0;
}

.logo {
  font-weight: bold;
  font-size: 1.5rem;
}

.main-nav a {
  margin: 0 1rem;
  text-decoration: none;
  color: #aaaaaa;
  transition: color 0.2s;
}

.main-nav a:hover {
  color: #ffffff;
}

.content {
  padding: 2rem;
}
</style>