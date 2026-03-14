<template>
  <div class="app-container">
    <AppHeader v-if="authStore.isAuthenticated" />
    <main>
      <RouterView />
    </main>
    <AppFooter v-if="authStore.isAuthenticated" />
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router'
import AppHeader from './components/AppHeader.vue'
import AppFooter from './components/AppFooter.vue'
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
  display: flex;
  flex-direction: column;
}

main {
  flex: 1;
  padding-top: 70px; /* Height of navbar */
}
</style>
