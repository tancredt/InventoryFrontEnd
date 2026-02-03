<template>
  <div class="welcome-page">
    <div v-if="!authStore.isAuthenticated" class="welcome-container">
      <h1>Welcome to the Hazmat Equipment Inventory System</h1>
      <p>Please log in to manage your equipment.</p>
      <LoginForm />
    </div>

    <div v-else class="welcome-container">
      <h1>Welcome to the Hazmat Equipment Inventory System</h1>
      <p>Hello, {{ authStore.currentUser?.username }}! Use the navigation above to manage your equipment.</p>
      <button @click="handleLogout" class="btn btn-secondary">Logout</button>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { useAuthStore } from '../stores/auth';
import LoginForm from './LoginForm.vue';

const router = useRouter();
const authStore = useAuthStore();

// Handle logout
const handleLogout = async () => {
  await authStore.logout();
  router.push('/');
};
</script>

<style scoped>
.welcome-page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.welcome-container {
  max-width: 1200px;
  margin: 2rem auto;
  padding: 0 2rem;
  text-align: center;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
  text-align: center;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}

.btn-secondary:hover {
  background-color: #5a6268;
}

@media (max-width: 768px) {
  .welcome-container {
    padding: 0 1rem;
    margin-top: 1rem;
  }
}
</style>