<template>
  <div class="app-container">
    <Navbar v-if="authStore.isAuthenticated" />
    <main :class="{ 'has-navbar': authStore.isAuthenticated }">
      <RouterView />
    </main>
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router'
import Navbar from './components/Navbar.vue'
import { useAuthStore } from './stores/auth'
import { onMounted } from 'vue'

const authStore = useAuthStore()

onMounted(async () => {
  await authStore.checkAuth()
})
</script>

<style>
.app-container {
  min-height: 100vh;
}

main.has-navbar {
  padding-top: 70px; /* Height of navbar + padding */
}
</style>
