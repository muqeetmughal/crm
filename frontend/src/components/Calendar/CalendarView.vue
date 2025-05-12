<template>
  <div>
    <div class="p-5">
      <ul>
        <li v-for="appointment in appointments.data" :key="appointment.name">
        <strong>{{ appointment.customer_name }}</strong> with {{ appointment.appointment_with }}
        <br />
        Scheduled Time: {{ appointment.scheduled_time }}
        <br />
        Status: {{ appointment.status }}
        <br />
        Email: {{ appointment.customer_email }}
        </li>
      </ul>
      <button @click="appointments.fetch()">Refresh</button>
    </div>



  </div>
</template>

<script setup>
import { createListResource } from 'frappe-ui'

const appointments = createListResource({
  doctype: 'Appointment',
  fields: "*",
  filters: {
    status: 'Open'
  },
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
    console.log(data)
    return data.map(item => ({
      ...item,
      open: false
    }))
  }
})
</script>

