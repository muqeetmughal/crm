<script setup>
import { reactive, ref, watchEffect } from 'vue'
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'
import interactionPlugin from '@fullcalendar/interaction'
import AppointmentModal from '@/components/Modals/AppointmentModal.vue'
import QuickEntryModal from '@/components/Modals/QuickEntryModal.vue'
import { createListResource } from 'frappe-ui'
import tippy from 'tippy.js'
import 'tippy.js/dist/tippy.css'; // Optional: Import default CSS
const modals = reactive({
  showAppointmentModal: false,
  showQuickEntryModal: false,
})

const isEditMode = ref(false)
const form = reactive({
  title: '',
  date: '',
})

// Frappe resource for appointments
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
  transform(data) {
    return data.map((item) => ({
      id: item.name,
      title: item.customer_name || 'No Title',
      date: item.scheduled_time ? new Date(item.scheduled_time) : new Date(),
    }))
  },
})

// Setup FullCalendar options
const calendarOptions = reactive({
  plugins: [dayGridPlugin, timeGridPlugin, interactionPlugin],
  initialView: 'dayGridMonth',
  weekends: true,
  headerToolbar: {
    left: 'prev,next today',
    center: 'title',
    right: 'dayGridMonth,timeGridWeek,timeGridDay',
  },
  eventColor: '#378006',
  events: [], // Will be set reactively below
  dateClick: handleDateClick,
  eventDidMount: (info) => {
    tippy(info.el, {
      content: info.event.title, // Customize this to show more details
      placement: 'top',
      theme: 'light-border',
    });
  },
})

// 💡 Dynamically sync calendar events with appointments.data
watchEffect(() => {
  if (!appointments.loading) {
    calendarOptions.events = appointments.data
  }
})

function handleDateClick(info) {
  form.date = info.dateStr
  modals.showAppointmentModal = true
}

function handleSubmit() {
  console.log('Create', form)

  if (isEditMode.value) {
    // Handle update
  } else {
    // Handle creation
    // ⚠️ You need to call a Frappe API here to actually save the event.
    // After save, trigger a reload:
    appointments.reload()
  }

  closeModal()
}

function closeModal() {
  modals.showAppointmentModal = false
  isEditMode.value = false
  form.title = ''
  form.date = ''
}
</script>




<template>
  <div>
    <AppointmentModal
    v-if="modals.showAppointmentModal"
      v-model="modals.showAppointmentModal"
      :quickEntry="modals.showQuickEntryModal"
      :defaults="form"
      @submit="handleSubmit"
    />

    <QuickEntryModal
      v-if="modals.showQuickEntryModal"
      v-model="modals.showQuickEntryModal"
      doctype="Appointment"
    />

    <FullCalendar
      v-if="!appointments.loading && appointments.data"
      :options="calendarOptions"
    />
  </div>
</template>
