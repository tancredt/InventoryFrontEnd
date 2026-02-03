<template>
  <div class="maintenance-edit-page">

    <div class="page-container">
      <h1>{{ isNewMaintenance ? 'Add New Maintenance' : 'Edit Maintenance' }}</h1>

      <div class="form-container">
        <form @submit.prevent="saveMaintenance" class="maintenance-form">
          <div class="form-grid">
            <div class="form-group">
              <label for="detector">Detector:</label>
              <input type="text" id="detector" :value="detectorLabel" disabled class="form-control readonly">
            </div>

            <div class="form-group">
              <label for="maintenanceType">Maintenance Type *:</label>
              <select id="maintenanceType" v-model="maintenance.maintenance_type" class="form-control" :disabled="isComplete" required>
                <option value="">Select Maintenance Type</option>
                <option v-for="choice in maintenanceTypeChoices" :key="choice.value" :value="choice.value">
                  {{ choice.label }}
                </option>
              </select>
            </div>

            <div class="form-group">
              <label for="dateDue">Date Due *:</label>
              <input type="date" id="dateDue" v-model="maintenance.date_due" class="form-control" :disabled="isComplete" required>
            </div>

            <div class="form-group">
              <label for="status">Status *:</label>
              <select id="status" v-model="maintenance.status" class="form-control" :disabled="isComplete" required>
                <option value="">Select Status</option>
                <option v-for="choice in maintenanceStatusChoices" :key="choice.value" :value="choice.value">
                  {{ choice.label }}
                </option>
              </select>
            </div>

            <div class="divider"></div>

            <div class="form-group">
              <label for="datePerformed">Date Performed:</label>
              <input type="date" id="datePerformed" v-model="maintenance.date_performed" class="form-control" :disabled="isComplete">
            </div>

            <div class="form-group">
              <label for="performedBy">Performed By:</label>
              <input type="text" id="performedBy" v-model="maintenance.performed_by" class="form-control" :disabled="isComplete">
            </div>

            <div class="form-group full-width">
              <label for="notes">Notes:</label>
              <textarea id="notes" v-model="maintenance.notes" class="form-control" :disabled="isComplete" rows="3"></textarea>
            </div>
          </div>

          <div class="form-actions">
            <button type="submit" class="btn btn-primary" :disabled="isComplete">{{ isNewMaintenance ? 'Add Maintenance' : 'Update Maintenance' }}</button>
            <router-link :to="returnTo" class="btn btn-secondary">Cancel</router-link>
          </div>
        </form>
      </div>
    </div>

    <!-- Success Dialog -->
    <div v-if="showSuccessDialog" class="dialog-overlay" @click="closeDialog">
      <div class="dialog-box" @click.stop>
        <h3>Success!</h3>
        <p>Maintenance record has been {{ isNewMaintenance ? 'added' : 'updated' }} successfully.</p>
        <div class="dialog-actions">
          <button @click="closeDialogAndReturn" class="btn btn-primary">OK</button>
        </div>
      </div>
    </div>

    <!-- Status Change Dialog -->
    <div v-if="showStatusChangeDialog" class="dialog-overlay" @click="handleStatusChangeDialog(false)">
      <div class="dialog-box" @click.stop>
        <h3>Change Status?</h3>
        <p>You've entered a date performed. Would you like to change the status to Complete?</p>
        <div class="dialog-actions">
          <button @click="handleStatusChangeDialog(true)" class="btn btn-primary">Yes</button>
          <button @click="handleStatusChangeDialog(false)" class="btn btn-secondary">No</button>
        </div>
      </div>
    </div>

    <!-- Missing Date Performed Warning Dialog -->
    <div v-if="showMissingDatePerformedWarning" class="dialog-overlay" @click="closeMissingDatePerformedWarning">
      <div class="dialog-box" @click.stop>
        <h3>Missing Date Performed</h3>
        <p>You have set the status to Complete but have not entered a date performed. Please enter a date performed before completing the maintenance.</p>
        <div class="dialog-actions">
          <button @click="closeMissingDatePerformedWarning" class="btn btn-primary">OK</button>
        </div>
      </div>
    </div>

    <!-- Schedule New Maintenance Dialog -->
    <div v-if="showScheduleNewDialog" class="dialog-overlay" @click="handleScheduleNewDialog(false)">
      <div class="dialog-box" @click.stop>
        <h3>Schedule New Maintenance?</h3>
        <p>Do you wish to schedule a new maintenance?</p>
        <div class="dialog-actions">
          <button @click="handleScheduleNewDialog(true)" class="btn btn-primary">Yes</button>
          <button @click="handleScheduleNewDialog(false)" class="btn btn-secondary">No</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();

// State variables
const detectorId = ref(route.params.detectorId);
const maintenanceId = ref(route.params.maintenanceId);
const detectorLabel = ref('');
const maintenance = ref({
  detector: detectorId.value,
  maintenance_type: '',
  status: '',
  date_due: new Date().toISOString().split('T')[0],
  date_performed: '',
  performed_by: '',
  notes: ''
});
const maintenanceTypeChoices = ref([]);
const maintenanceStatusChoices = ref([]);
const showSuccessDialog = ref(false);
const showStatusChangeDialog = ref(false);
const showMissingDatePerformedWarning = ref(false);
const showScheduleNewDialog = ref(false);

// Check if we're creating a new maintenance or editing an existing one
const isNewMaintenance = computed(() => !route.params.maintenanceId || route.params.maintenanceId === 'new');

// Determine where to return to based on the referrer
const returnTo = computed(() => {
  // Check if the route has a 'from' parameter indicating where the user came from
  if (route.query.from === 'maintenances') {
    // If coming from the maintenances table, return to the maintenances page
    return '/maintenances';
  } else {
    // Otherwise, return to the detector details page (default behavior)
    return `/detectors/${detectorId.value}`;
  }
});

// Track the initial status when the form loads
const initialStatus = ref('');

// Check if maintenance status is Complete ('CP') and form was loaded with Complete status
const isComplete = computed(() => {
  // Disable inputs only if the form was initially loaded with Complete status
  // Allow editing when the user changes the status to Complete from another status
  return initialStatus.value === 'CP' && maintenance.value.status === 'CP';
});

// Fetch choice options from the API
const fetchChoices = async () => {
  try {
    // Fetch maintenance type choices
    const maintenanceTypeResponse = await fetch('/inventory/maintenance-types/');
    if (maintenanceTypeResponse.ok) {
      maintenanceTypeChoices.value = await maintenanceTypeResponse.json();
    }
    
    // Fetch maintenance status choices
    const maintenanceStatusResponse = await fetch('/inventory/maintenance-statuses/');
    if (maintenanceStatusResponse.ok) {
      maintenanceStatusChoices.value = await maintenanceStatusResponse.json();
    }
  } catch (error) {
    console.error('Error fetching choice options:', error);
  }
};

// Fetch maintenance details if editing existing
const fetchMaintenance = async () => {
  console.log(route.params.maintenanceId)
  // Only fetch if we're editing an existing maintenance (not creating a new one)
  if (route.params.maintenanceId && route.params.maintenanceId !== 'new') {
    try {
      const response = await fetch(`/inventory/maintenances/${route.params.maintenanceId}/`);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      const data = await response.json();
      maintenance.value = {
        ...data,
        detector: detectorId.value, // Ensure detector is always set to the current detector
        date_due: data.date_due ? new Date(data.date_due).toISOString().split('T')[0] : '',
        date_performed: data.date_performed ? new Date(data.date_performed).toISOString().split('T')[0] : ''
      };
    } catch (error) {
      console.error('Error fetching maintenance:', error);
    }
  }
};

// Fetch detector details
const fetchDetectorDetails = async () => {
  try {
    const response = await fetch(`/inventory/detectors/${detectorId.value}/`);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    detectorLabel.value = data.label;
  } catch (error) {
    console.error('Error fetching detector details:', error);
  }
};

// Save maintenance function
const saveMaintenance = async () => {
  try {
    // Validate required fields
    if (!maintenance.value.maintenance_type) {
      alert('Maintenance Type is required.');
      return;
    }

    if (!maintenance.value.date_due) {
      alert('Date Due is required.');
      return;
    }

    if (!maintenance.value.status) {
      alert('Status is required.');
      return;
    }

    // Validate date performed if it exists
    if (maintenance.value.date_performed && maintenance.value.date_due) {
      const datePerformed = new Date(maintenance.value.date_performed);
      const dateDue = new Date(maintenance.value.date_due);

      if (datePerformed < dateDue) {
        alert('Date Performed must be equal to or greater than Date Due.');
        return;
      }
    }

    // If status is Complete, date performed must be set
    if (maintenance.value.status === 'CP' && !maintenance.value.date_performed) {
      showMissingDatePerformedWarning.value = true;
      return;
    }

    // If we reach here, all required fields are filled and validated
    // Proceed with saving
    await performSave();
  } catch (error) {
    console.error('Error saving maintenance:', error);
    alert('Error saving maintenance: ' + error.message);
  }
};

// Function to handle the actual saving after dialog confirmation
const performSave = async () => {
  try {
   
    // Convert empty strings to null for date fields
    if (maintenance.value.date_performed === '') {
      maintenance.value.date_performed = null;
    }


    let response;
    if (isNewMaintenance.value) {
      // Creating a new maintenance
      response = await fetch('/inventory/maintenances/', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(maintenance.value)
      });
    } else {
      // Updating an existing maintenance
      response = await fetch(`/inventory/maintenances/${route.params.maintenanceId}/`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(maintenance.value)
      });
    }

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    // Check if status is Complete and date_performed is not null
    if (maintenance.value.status === 'CP' && maintenance.value.date_performed) {
      // Show dialog asking if user wants to schedule a new maintenance
      showScheduleNewDialog.value = true;
    } else {
      // Show success dialog
      showSuccessDialog.value = true;
    }
  } catch (error) {
    console.error('Error saving maintenance:', error);
    alert('Error saving maintenance: ' + error.message);
  }
};

// Function to handle the status change dialog response
const handleStatusChangeDialog = (shouldChange) => {
  if (shouldChange) {
    // Change status to Complete ('CP')
    maintenance.value.status = 'CP';
  }
  showStatusChangeDialog.value = false;
  // Now proceed with saving
};

// Watch for changes to date performed
watch(() => maintenance.value.date_performed, (newVal, oldVal) => {
  // If a date performed is being set and the status is not already 'Complete', validate the date first
  if (newVal && maintenance.value.status !== 'CP' && maintenance.value.date_due) {
    const datePerformed = new Date(newVal);
    const dateDue = new Date(maintenance.value.date_due);

    if (datePerformed >= dateDue) {
      // Date is valid, show the status change dialog
      showStatusChangeDialog.value = true;
    } else {
      // Date is invalid, show an error and clear the date performed
      alert('Date Performed must be equal to or greater than Date Due.');
      maintenance.value.date_performed = '';
    }
  }
});

// Close dialog and return to appropriate page based on where user came from
const closeDialogAndReturn = () => {
  showSuccessDialog.value = false;
  router.push(returnTo.value);
};

// Close dialog
const closeDialog = () => {
  showSuccessDialog.value = false;
};

// Close missing date performed warning dialog
const closeMissingDatePerformedWarning = () => {
  showMissingDatePerformedWarning.value = false;
};

// Function to handle the schedule new maintenance dialog response
const handleScheduleNewDialog = async (shouldSchedule) => {
  showScheduleNewDialog.value = false;

  if (shouldSchedule) {
    // Calculate date 6 months after the old date performed
    const oldDatePerformed = new Date(maintenance.value.date_performed);
    const sixMonthsAfterOldDate = new Date(oldDatePerformed);
    sixMonthsAfterOldDate.setMonth(sixMonthsAfterOldDate.getMonth() + 6);
    const futureDate = sixMonthsAfterOldDate.toISOString().split('T')[0];

    // Prepare the new maintenance data
    const newMaintenanceData = {
      detector: maintenance.value.detector,
      maintenance_type: maintenance.value.maintenance_type,
      status: 'SC', // Scheduled
      date_due: futureDate,
      date_performed: null,
      performed_by: ''
    };

    // Navigate to the new maintenance form with the pre-filled data
    // We'll pass the data as query parameters
    const query = new URLSearchParams({
      detector: newMaintenanceData.detector,
      maintenance_type: newMaintenanceData.maintenance_type,
      status: newMaintenanceData.status,
      date_due: newMaintenanceData.date_due
    }).toString();

    // Redirect to create a new maintenance with pre-filled data
    router.push(`/maintenances/${newMaintenanceData.detector}?${query}`);
  } else {
    // Just show the success dialog
    showSuccessDialog.value = true;
  }
};

// Initialize component
onMounted(async () => {
  // Load choices
  await fetchChoices();

  // Load detector details
  await fetchDetectorDetails();

  // If editing existing maintenance, load its details
  if (!isNewMaintenance.value) {
    await fetchMaintenance();
  } else {
    // If creating new maintenance, check for pre-filled data from query parameters
    const { detector, maintenance_type, status, date_due } = route.query;
    if (detector) maintenance.value.detector = parseInt(detector);
    if (maintenance_type) maintenance.value.maintenance_type = maintenance_type;
    if (status) maintenance.value.status = status;
    if (date_due) maintenance.value.date_due = date_due;
  }

  // Set the initial status after all data is loaded
  initialStatus.value = maintenance.value.status;
});

// Watch for route changes to handle pre-filled data
watch(() => route.query, (newQuery) => {
  if (isNewMaintenance.value) {
    const { detector, maintenance_type, status, date_due } = newQuery;
    if (detector) maintenance.value.detector = parseInt(detector);
    if (maintenance_type) maintenance.value.maintenance_type = maintenance_type;
    if (status) maintenance.value.status = status;
    if (date_due) maintenance.value.date_due = date_due;
  }
});
</script>

<style scoped>
.maintenance-edit-page {
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

.maintenance-form {
  display: flex;
  flex-direction: column;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  padding-right: 1rem; /* Add padding to the right side */
}

.form-group {
  margin-bottom: 0.5rem;
  min-width: 0; /* Allows flex/grid items to shrink below content size */
}

.form-group.full-width {
  grid-column: span 2; /* Span across both columns */
}

.divider {
  grid-column: span 2; /* Span across both columns */
  height: 1px;
  background-color: #ccc;
  margin: 1rem 0;
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

.form-control:disabled {
  background-color: #f8f9fa;
  cursor: not-allowed;
  opacity: 0.7;
}

.form-control:focus {
  outline: none;
  border-color: #42b883;
  box-shadow: 0 0 0 2px rgba(66, 184, 131, 0.2);
}

textarea.form-control {
  resize: vertical;
  min-height: 80px;
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

  .form-group.full-width {
    grid-column: span 1; /* Reset to single column on small screens */
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