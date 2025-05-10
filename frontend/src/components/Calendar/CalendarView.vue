<template>
  <div class="flex h-screen flex-col overflow-hidden p-5">
    <Calendar
      :config="calendarConfig"
      :events="calendarEvents"
      @create="handleCreate"
      @update="handleUpdate"
      @delete="handleDelete"
    />
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { Calendar, createListResource } from 'frappe-ui'

// Define the calendar configuration
const calendarConfig = {
  defaultMode: 'Month',
  isEditMode: true,
  eventIcons: {},
  allowCustomClickEvents: true,
  redundantCellHeight: 100,
  enableShortcuts: false,
}

// Initialize the list resource for fetching events
const appointments = createListResource({
  doctype: 'Appointment',
  fields: "*",
  // orderBy: 'created desc',
  start: 0,
  pageLength: 100,
})

// Reactive reference to hold the formatted events
const calendarEvents = ref([])

// Watch for changes in the appointments list and map them to the calendar format
watch(
  () => appointments.list,
  (newList) => {
    calendarEvents.value = Array.isArray(newList)
      ? newList.map((doc) => ({
            id: doc.name,
            title: doc.customer_name,
            participant: doc.appointment_with,
            venue: doc.party || 'N/A',
            fromDate: doc.scheduled_time,
            toDate: doc.scheduled_time, // Assuming single-day events
            color: doc.status === 'Open' ? 'green' : 'red',
            isFullDay: false, // Assuming not full-day events
        }))
      : []
  },
  { immediate: true }
)

// Fetch the appointments data
appointments.fetch()

// Event handlers
function handleCreate(event) {
  console.log('Create Event:', event)
  // Implement creation logic here
}

function handleUpdate(event) {
  console.log('Update Event:', event)
  // Implement update logic here
}

function handleDelete(eventID) {
  console.log('Delete Event ID:', eventID)
  // Implement deletion logic here
}
</script>
