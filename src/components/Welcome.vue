<template>
  <div class="welcome-page">
    <!-- Static informational section for Websense content filtering -->
    <section class="info-section">
      <div class="info-container">
        <h2>About the FRV Hazmat Equipment Inventory System</h2>
        <p>
          The Fire Rescue Victoria Hazmat Equipment Inventory System is an internal application 
          designed to track and manage hazardous materials detection equipment across all FRV stations 
          and facilities. This system ensures that critical safety equipment is properly maintained, 
          calibrated, and available for emergency response operations.
        </p>
      </div>
    </section>

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

    <!-- Equipment overview section -->
    <section class="equipment-section">
      <div class="equipment-container">
        <h3>Equipment Tracked by This System</h3>
        <div class="equipment-grid">
          <div class="equipment-card">
            <h4>Gas Detectors</h4>
            <p>Multi-gas detectors including MicroRAE, MultiRAE, XAM8000, MSA Altair, and AreaRAE models for detecting hazardous gases in emergency situations.</p>
          </div>
          <div class="equipment-card">
            <h4>Calibration Cylinders</h4>
            <p>Calibration gas cylinders with known concentrations for testing and calibrating detector accuracy.</p>
          </div>
          <div class="equipment-card">
            <h4>Sensors</h4>
            <p>Electrochemical and PID sensors for detecting specific gases including CO, H2S, O2, LEL, and VOCs.</p>
          </div>
          <div class="equipment-card">
            <h4>Maintenance Records</h4>
            <p>Scheduled service records, bump tests, calibrations, and fault reports for all equipment.</p>
          </div>
        </div>
      </div>
    </section>

    <AppFooter />
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { useAuthStore } from '../stores/auth';
import LoginForm from './LoginForm.vue';
import AppFooter from './AppFooter.vue';

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

.info-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 2rem;
  margin-bottom: 2rem;
}

.info-container {
  max-width: 1200px;
  margin: 0 auto;
}

.info-section h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
}

.info-section p {
  font-size: 1rem;
  line-height: 1.6;
  max-width: 900px;
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

.equipment-section {
  background: #f5f5f5;
  padding: 2rem;
  margin-top: 2rem;
}

.equipment-container {
  max-width: 1200px;
  margin: 0 auto;
}

.equipment-container h3 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 2rem;
  font-size: 1.3rem;
}

.equipment-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
}

.equipment-card {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.equipment-card h4 {
  color: #667eea;
  margin-bottom: 0.75rem;
  font-size: 1.1rem;
}

.equipment-card p {
  color: #666;
  font-size: 0.9rem;
  line-height: 1.5;
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

  .info-section {
    padding: 1.5rem;
  }

  .info-section h2 {
    font-size: 1.2rem;
  }

  .equipment-section {
    padding: 1.5rem;
  }
}
</style>