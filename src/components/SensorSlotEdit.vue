<template>
  <div class="sensorslot-edit-page">

    <div class="page-container">
      <h1>Edit Sensor Slot</h1>

      <div class="form-container">
        <form @submit.prevent="saveSensorSlot" class="sensorslot-form">
          <div class="form-grid">
            <div class="form-group">
              <label for="detector">Detector:</label>
              <input type="text" id="detector" :value="detectorLabel" disabled class="form-control readonly">
            </div>

            <div class="form-group">
              <label for="sensorType">Sensor Type:</label>
              <input type="text" id="sensorType" :value="sensorTypeLabel" disabled class="form-control readonly">
            </div>

            <div class="form-group">
              <label for="currentSensor">Current Sensor:</label>
              <input type="text" id="currentSensor" :value="currentSensorLabel" disabled class="form-control readonly">
            </div>

            <div class="form-group">
              <label for="gas">Gas:</label>
              <input type="text" id="gas" :value="gas1Label" disabled class="form-control readonly">
            </div>

            <div class="form-group">
              <label for="newSensor">New Sensor:</label>
              <select id="newSensor" v-model="selectedSensor" class="form-control">
                <option value="">Select a sensor</option>
                <option v-for="sensor in availableSensors" :key="sensor.id" :value="sensor.id">
                  {{ sensor.serial }} ({{ sensor.expiry_date || 'No expiry' }})
                </option>
              </select>
            </div>

            <div class="form-group">
              <label for="installDate">Install Date:</label>
              <input type="date" id="installDate" v-model="installDate" class="form-control">
            </div>
          </div>

          <div class="form-actions">
            <button type="submit" class="btn btn-primary">Update Sensor Slot</button>
            <router-link :to="`/detectors/${detectorId}`" class="btn btn-secondary">Cancel</router-link>
          </div>
        </form>
      </div>
    </div>

    <!-- Success Dialog -->
    <div v-if="showSuccessDialog" class="dialog-overlay" @click="closeDialog">
      <div class="dialog-box" @click.stop>
        <h3>Success!</h3>
        <p>Sensor slot has been updated successfully.</p>
        <div class="dialog-actions">
          <button @click="closeDialogAndReturn" class="btn btn-primary">OK</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();

// State variables
const detectorId = ref(route.params.detectorId);
// Check if we have sensorTypeId (new route format) or slotId (old route format)
const slotId = ref(route.params.sensorTypeId || route.params.slotId);
const detectorLabel = ref('');
const sensorTypeLabel = ref('');
const gas1Label = ref('');
const currentSensorLabel = ref('');
const selectedSensor = ref('');
const installDate = ref(new Date().toISOString().split('T')[0]);
const availableSensors = ref([]);
const showSuccessDialog = ref(false);

// Fetch the sensor slot details and related data
const fetchSensorSlotDetails = async () => {
  try {
    let slotData;

    // Check if slotId is actually a sensor type ID (new route format)
    // If route has sensorTypeId, we need to find the slot for this detector and sensor type
    if (route.params.sensorTypeId) {
      // Fetch all sensor slots for this detector
      const slotsResponse = await fetch(`/inventory/sensorslots/?detector=${detectorId.value}`);
      if (!slotsResponse.ok) {
        throw new Error(`HTTP error! status: ${slotsResponse.status}`);
      }
      const allSlots = await slotsResponse.json();

      // Find the slot that matches the sensor type
      // Prefer slots that don't have a sensor assigned yet, otherwise pick the first one
      const matchingSlots = allSlots.filter(slot => slot.sensor_type == route.params.sensorTypeId);
      if (matchingSlots.length === 0) {
        throw new Error(`No sensor slot found for detector ${detectorId.value} with sensor type ${route.params.sensorTypeId}`);
      }

      // If there are multiple slots with the same sensor type,
      // prefer the one that doesn't have a sensor assigned yet
      let matchingSlot = matchingSlots.find(slot => !slot.sensor);
      if (!matchingSlot) {
        // If all slots have sensors, just use the first one
        matchingSlot = matchingSlots[0];
      }

      const foundSlotData = matchingSlot;
      // Update the slotId to the actual slot ID
      slotId.value = foundSlotData.id;
      slotData = foundSlotData;
    } else {
      // Fetch the sensor slot details using the slot ID (old route format)
      const slotResponse = await fetch(`/inventory/sensorslots/${slotId.value}`);
      if (!slotResponse.ok) {
        throw new Error(`HTTP error! status: ${slotResponse.status}`);
      }
      slotData = await slotResponse.json();
    }

    // Fetch detector details
    const detectorResponse = await fetch(`/inventory/detectors/${detectorId.value}/`);
    if (detectorResponse.ok) {
      const detectorData = await detectorResponse.json();
      detectorLabel.value = detectorData.label;
    }

    // Fetch sensor type details
    const sensorTypeResponse = await fetch(`/inventory/sensortypes/${slotData.sensor_type}/`);
    if (sensorTypeResponse.ok) {
      const sensorTypeData = await sensorTypeResponse.json();
      sensorTypeLabel.value = sensorTypeData.part_number;
      gas1Label.value = sensorTypeData.sensorgas;
    }

    // Set current sensor if exists
    if (slotData.sensor) {
      const sensorResponse = await fetch(`/inventory/sensors/${slotData.sensor}/`);
      if (sensorResponse.ok) {
        const sensorData = await sensorResponse.json();
        currentSensorLabel.value = sensorData.serial || 'N/A';
        // Don't pre-select the current sensor since we want the user to select a new one
      }
    } else {
      currentSensorLabel.value = 'None';
    }

    // Fetch available sensors of the same type that are in stock
    const sensorsResponse = await fetch(`/inventory/sensors/?sensor_type=${slotData.sensor_type}&status=IS`);
    if (sensorsResponse.ok) {
      availableSensors.value = await sensorsResponse.json();
    }
  } catch (error) {
    console.error('Error fetching sensor slot details:', error);
  }
};

// Get sensor status display
const getSensorStatusLabel = (statusValue) => {
  const statusMap = {
    'OP': 'Operational',
    'IS': 'In Stock',
    'OO': 'On Order',
    'DC': 'Decommissioned'
  };
  return statusMap[statusValue] || statusValue;
};

watch(selectedSensor, () => {
  console.log(selectedSensor.value)
})
// Save sensor slot update
const saveSensorSlot = async () => {
  try {
    // If a new sensor was selected, update the sensor's detector and install date
    if (selectedSensor.value) {
      
      const sensorPayload = {
        detector: parseInt(detectorId.value),  // Convert string ID to integer
        status: 'OP'  // Set status to Operational
      };

      if (installDate.value) {
        sensorPayload.install_date = installDate.value;
      }

      // Update the sensor's detector and install date
      const sensorResponse = await fetch(`/inventory/sensors/${selectedSensor.value}/`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(sensorPayload)
      });

      if (!sensorResponse.ok) {
        throw new Error(`HTTP error! status: ${sensorResponse.status}`);
      }
    }

    // Show success dialog
    showSuccessDialog.value = true;
  } catch (error) {
    console.error('Error updating sensor slot:', error);
    alert('Error updating sensor slot: ' + error.message);
  }
};

// Close dialog and return to detector details
const closeDialogAndReturn = () => {
  showSuccessDialog.value = false;
  router.push(`/detectors/${detectorId.value}`);
};

// Close dialog
const closeDialog = () => {
  showSuccessDialog.value = false;
};

// Initialize component
onMounted(async () => {
  await fetchSensorSlotDetails();
});
</script>

<style scoped>
.sensorslot-edit-page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.page-container {
  max-width: 1200px;
  margin: 1rem auto;
  padding: 0 2rem;
  flex: 1;
}

h1 {
  color: #2c3e50;
  margin-bottom: 1rem;
}

.form-container {
  background: white;
  border-radius: 8px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.sensorslot-form {
  display: flex;
  flex-direction: column;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  padding-right: 1rem;
}

.form-group {
  margin-bottom: 0.5rem;
  min-width: 0; /* Allows flex/grid items to shrink below content size */
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: #333;
}

.form-control {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  box-sizing: border-box; /* Include padding in width calculation */
}

.form-control.readonly {
  background-color: #f8f9fa;
  cursor: not-allowed;
}

.form-control:focus {
  outline: none;
  border-color: #42b883;
  box-shadow: 0 0 0 2px rgba(66, 184, 131, 0.2);
}

.form-actions {
  display: flex;
  gap: 1rem;
  margin-top: 2rem;
  padding-top: 1rem;
  border-top: 1px solid #eee;
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

.btn-primary {
  background-color: #42b883;
  color: white;
}

.btn-primary:hover:not(:disabled) {
  background-color: #36966d;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}

.btn-secondary:hover {
  background-color: #5a6268;
}

.dialog-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.dialog-box {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  text-align: center;
  max-width: 400px;
  width: 90%;
  z-index: 1001;
}

.dialog-box h3 {
  margin-top: 0;
  color: #2c3e50;
}

.dialog-actions {
  margin-top: 1.5rem;
  display: flex;
  justify-content: center;
  gap: 1rem;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .form-grid {
    grid-template-columns: 1fr; /* Single column on smaller screens */
    gap: 1rem;
  }

  .page-container {
    padding: 0 1rem;
    margin: 1rem auto;
  }

  .form-container {
    padding: 1rem;
  }
}
</style>