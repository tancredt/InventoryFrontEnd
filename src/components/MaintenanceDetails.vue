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
        <p>You've entered a date performed. Would you like to change the status to Closed?</p>
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
        <p>You have set the status to Closed but have not entered a date performed. Please enter a date performed before completing the maintenance.</p>
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

    <!-- Maintenance Tasks Section -->
    <div class="page-container">
      <div class="tasks-section">
        <h2>Maintenance Tasks</h2>
        
        <div class="tasks-header">
          <div class="task-type-select">
            <label for="taskType">Add Task:</label>
            <select id="taskType" v-model="selectedTaskType" class="form-control" :disabled="isComplete">
              <option value="">Select a task type</option>
              <option v-for="choice in maintenanceTaskTypeChoices" :key="choice.value" :value="choice.value">
                {{ choice.label }}
              </option>
            </select>
            <button @click="addTask" class="btn btn-primary btn-add-task" :disabled="!selectedTaskType || isComplete">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="12" y1="5" x2="12" y2="19"></line>
                <line x1="5" y1="12" x2="19" y2="12"></line>
              </svg>
              Add
            </button>
          </div>
        </div>

        <div class="tasks-list">
          <div v-if="maintenanceTasks.length === 0" class="no-tasks">
            No tasks added yet.
          </div>
          <div v-else class="tasks-table">
            <div class="task-row task-header-row">
              <div class="task-cell">Task Type</div>
              <div class="task-cell">Actions</div>
            </div>
            <div v-for="task in maintenanceTasks" :key="task.id" class="task-row">
              <div class="task-cell">
                {{ getTaskTypeLabel(task.task_type) }}
              </div>
              <div class="task-cell task-actions">
                <button @click="removeTask(task)" class="btn btn-danger btn-sm" :disabled="isComplete" title="Remove task">
                  <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <polyline points="3 6 5 6 21 6"></polyline>
                    <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { get, post, put, del } from '@/utils/api';

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
const maintenanceTaskTypeChoices = ref([]);
const maintenanceTasks = ref([]);
const selectedTaskType = ref('');
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

// Check if maintenance status is Closed ('CL') and form was loaded with Closed status
const isComplete = computed(() => {
  // Disable inputs only if the form was initially loaded with Closed status
  // Allow editing when the user changes the status to Closed from another status
  return initialStatus.value === 'CL' && maintenance.value.status === 'CL';
});

// Fetch choice options from the API
const fetchChoices = async () => {
  try {
    // Fetch maintenance type choices
    const maintenanceTypeResult = await get('/api/inventory/maintenance-types/');
    if (maintenanceTypeResult.ok) {
      maintenanceTypeChoices.value = maintenanceTypeResult.data;
    }

    // Fetch maintenance status choices
    const maintenanceStatusResult = await get('/api/inventory/maintenance-statuses/');
    if (maintenanceStatusResult.ok) {
      maintenanceStatusChoices.value = maintenanceStatusResult.data;
    }

    // Fetch maintenance task type choices
    const maintenanceTaskTypeResult = await get('/api/inventory/maintenance-task-types/');
    if (maintenanceTaskTypeResult.ok) {
      maintenanceTaskTypeChoices.value = maintenanceTaskTypeResult.data;
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
      const result = await get(`/api/inventory/maintenances/${route.params.maintenanceId}/`);
      if (!result.ok) {
        throw new Error(`HTTP error! status: ${result.status}`);
      }
      const data = result.data;
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
    const result = await get(`/api/inventory/detectors/${detectorId.value}/`);
    if (!result.ok) {
      throw new Error(`HTTP error! status: ${result.status}`);
    }
    detectorLabel.value = result.data.label;
  } catch (error) {
    console.error('Error fetching detector details:', error);
  }
};

// Get task type label from choices
const getTaskTypeLabel = (taskTypeValue) => {
  const choice = maintenanceTaskTypeChoices.value.find(c => c.value === taskTypeValue);
  return choice ? choice.label : taskTypeValue;
};

// Fetch maintenance tasks for the current maintenance
const fetchMaintenanceTasks = async () => {
  // Only fetch if we have a maintenance ID (editing existing maintenance)
  if (route.params.maintenanceId && route.params.maintenanceId !== 'new') {
    try {
      const result = await get(`/api/inventory/maintenancetasks/?maintenance=${route.params.maintenanceId}`);
      if (!result.ok) {
        throw new Error(`HTTP error! status: ${result.status}`);
      }
      maintenanceTasks.value = result.data;
    } catch (error) {
      console.error('Error fetching maintenance tasks:', error);
    }
  }
};

// Add a new task
const addTask = async () => {
  if (!selectedTaskType.value) return;

  try {
    const result = await post('/api/inventory/maintenancetasks/', {
      maintenance: isNewMaintenance.value ? null : parseInt(route.params.maintenanceId),
      task_type: selectedTaskType.value
    });

    if (!result.ok) {
      throw new Error(result.data.detail || 'Failed to add task');
    }

    const newTask = result.data;
    
    // If we're creating a new maintenance, we need to associate the task after save
    if (isNewMaintenance.value) {
      // Store the task temporarily, it will be associated after maintenance is saved
      maintenanceTasks.value.push({ ...newTask, id: `temp-${Date.now()}` });
    } else {
      maintenanceTasks.value.push(newTask);
    }
    
    selectedTaskType.value = '';
  } catch (error) {
    console.error('Error adding task:', error);
    alert('Error adding task: ' + error.message);
  }
};

// Remove a task
const removeTask = async (task) => {
  if (!confirm('Are you sure you want to remove this task?')) return;

  try {
    // If it's a temporary task (new maintenance not yet saved), just remove from local list
    if (task.id && typeof task.id === 'string' && task.id.startsWith('temp-')) {
      maintenanceTasks.value = maintenanceTasks.value.filter(t => t.id !== task.id);
      return;
    }

    // Otherwise, delete from the server
    const result = await del(`/api/inventory/maintenancetasks/${task.id}/`);

    if (!result.ok) {
      throw new Error('Failed to remove task');
    }

    maintenanceTasks.value = maintenanceTasks.value.filter(t => t.id !== task.id);
  } catch (error) {
    console.error('Error removing task:', error);
    alert('Error removing task: ' + error.message);
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

    // If status is Closed, date performed must be set
    if (maintenance.value.status === 'CL' && !maintenance.value.date_performed) {
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

    let result;
    if (isNewMaintenance.value) {
      // Creating a new maintenance
      result = await post('/api/inventory/maintenances/', maintenance.value);
    } else {
      // Updating an existing maintenance
      result = await put(`/api/inventory/maintenances/${route.params.maintenanceId}/`, maintenance.value);
    }

    if (!result.ok) {
      throw new Error(`HTTP error! status: ${result.status}`);
    }

    // Check if status is Closed and date_performed is not null
    if (maintenance.value.status === 'CL' && maintenance.value.date_performed) {
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
    // Change status to Closed ('CL')
    maintenance.value.status = 'CL';
  }
  showStatusChangeDialog.value = false;
  // Now proceed with saving
};

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

  // If editing existing maintenance, load its details and tasks
  if (!isNewMaintenance.value) {
    await fetchMaintenance();
    await fetchMaintenanceTasks();
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

/* Tasks Section Styles */
.tasks-section {
  background: white;
  border-radius: 8px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  margin-top: 1rem;
}

.tasks-section h2 {
  color: #2c3e50;
  margin-bottom: 1rem;
  font-size: 1.25rem;
}

.tasks-header {
  margin-bottom: 1rem;
}

.task-type-select {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.task-type-select label {
  font-weight: 600;
  color: #333;
  white-space: nowrap;
}

.task-type-select select {
  flex: 1;
  min-width: 200px;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.btn-add-task {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.25rem;
  white-space: nowrap;
}

.tasks-list {
  margin-top: 1rem;
}

.no-tasks {
  text-align: center;
  color: #6c757d;
  padding: 2rem;
  background-color: #f8f9fa;
  border-radius: 4px;
}

.tasks-table {
  border: 1px solid #ddd;
  border-radius: 4px;
  overflow: hidden;
}

.task-row {
  display: grid;
  grid-template-columns: 1fr 100px;
  border-bottom: 1px solid #ddd;
}

.task-row:last-child {
  border-bottom: none;
}

.task-header-row {
  background-color: #f8f9fa;
  font-weight: 600;
  color: #333;
}

.task-cell {
  padding: 0.75rem;
  display: flex;
  align-items: center;
}

.task-cell:first-child {
  border-right: 1px solid #ddd;
}

.task-actions {
  justify-content: center;
  gap: 0.5rem;
}

.btn-sm {
  padding: 0.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-danger {
  background-color: #dc3545;
  color: white;
}

.btn-danger:hover:not(:disabled) {
  background-color: #c82333;
}

.btn-danger:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
</style>
