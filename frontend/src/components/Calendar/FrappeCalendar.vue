<template>
  <div class="flex h-screen flex-col overflow-hidden p-5">
    <div v-if="appointments.loading">Loading appointments...</div>

    <Calendar
    v-if="!appointments.loading && appointments.data"

      :config="{
        defaultMode: 'Month',
        isEditMode: true,
        eventIcons: {},
        allowCustomClickEvents: true,
        redundantCellHeight: 100,
        enableShortcuts: false,
      }"
      :events="appointments.data"
      @create="(event) => console.log('createEvent', event)"
      @update="(event) => console.log('updateEvent', event)"
      @delete="(eventID) => console.log('deleteEvent', eventID)"
    />
  </div>
</template>


<script setup>
import { createListResource, Calendar } from 'frappe-ui'

const appointments = createListResource({
  doctype: 'Appointment',
  fields: '*',
  filters: {},
  orderBy: 'creation desc',
  start: 0,
  pageLength: 20,
  cache: 'appointments',
  auto: true,
  onError(error) {
    console.error('Error fetching appointments:', error)
  },
  onSuccess(data) {
    console.log('Fetched appointments:', data)
  },
  transform(data) {
    return data.map((item) => ({
      id: item.name,
      title: item.customer_name || 'No Title',
      description: item.description || 'No Description',
      fromDate: item.scheduled_time,
      toDate: item.ends_on || item.starts_o1n, // Use ends_on if available, otherwise fallback to starts_on
      color: item.color, // Default color if not provided
      allDay: !!item.all_day,
    }))
  },
})

</script>
