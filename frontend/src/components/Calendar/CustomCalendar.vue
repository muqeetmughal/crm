<template>
  <div class="p-4 h-full w-full">
    <!-- Header -->
    <div class="flex justify-between items-center mb-4">
      <div class="flex space-x-2">
        <button @click="goToToday" class="px-3 py-1 bg-blue-500 text-white rounded">Today</button>
        <button @click="goToPrev" class="px-3 py-1 bg-gray-300 rounded">Prev</button>
        <button @click="goToNext" class="px-3 py-1 bg-gray-300 rounded">Next</button>
      </div>
      <h2 class="text-xl font-bold">{{ formattedTitle }}</h2>
      <div class="flex space-x-2">
        <button v-for="v in ['day', 'week', 'month']"
                :key="v"
                @click="view = v"
                :class="view === v ? 'bg-blue-500 text-white' : 'bg-gray-200'"
                class="px-3 py-1 rounded capitalize">
          {{ v }}
        </button>
      </div>
    </div>

    <!-- Calendar View -->
    <div v-if="view === 'month'" class="h-full">
      <div class="grid grid-cols-7 gap-1 text-center font-semibold">
        <div v-for="d in daysShort" :key="d">{{ d }}</div>
      </div>
      <div class="grid grid-cols-7 gap-1 mt-2 text-sm h-full">
        <div v-for="cell in monthCells" :key="cell.date" class="h-full border rounded p-1">
          <div :class="{ 'text-gray-400': !cell.currentMonth }">{{ cell.date.date() }}</div>
          <div v-for="event in events.filter(e => dayjs(e.scheduled_time).isSame(cell.date, 'day'))"
               :key="event.name"
               class="text-xs bg-blue-100 text-blue-700 rounded p-1 mt-1">
            {{ event.customer_name }}
          </div>
        </div>
      </div>
    </div>

    <div v-else-if="view === 'week'" class="grid grid-cols-7 gap-1 text-sm h-full">
      <div v-for="day in weekDays" :key="day.format()" class="border p-4 rounded h-full">
        <div class="font-semibold">{{ day.format('ddd, MMM D') }}</div>
        <div v-for="event in events.filter(e => dayjs(e.scheduled_time).isSame(day, 'day'))"
             :key="event.name"
             class="text-xs bg-blue-100 text-blue-700 rounded p-1 mt-1">
          {{ event.customer_name }}
        </div>
      </div>
    </div>

    <div v-else-if="view === 'day'" class="border rounded p-4 text-sm h-full">
      <div class="font-semibold">{{ currentDate.format('dddd, MMMM D, YYYY') }}</div>
      <div v-for="event in events.filter(e => dayjs(e.scheduled_time).isSame(currentDate, 'day'))"
           :key="event.name"
           class="text-xs bg-blue-100 text-blue-700 rounded p-1 mt-4">
        {{ event.customer_name }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import dayjs from 'dayjs'
import weekday from 'dayjs/plugin/weekday'
import isoWeek from 'dayjs/plugin/isoWeek'
import weekOfYear from 'dayjs/plugin/weekOfYear'

dayjs.extend(weekday)
dayjs.extend(isoWeek)
dayjs.extend(weekOfYear)

const view = ref('month')
const currentDate = ref(dayjs())

const daysShort = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']

const formattedTitle = computed(() => {
  switch (view.value) {
    case 'day': return currentDate.value.format('MMMM D, YYYY')
    case 'week':
      const start = currentDate.value.startOf('week')
      const end = currentDate.value.endOf('week')
      return `${start.format('MMM D')} - ${end.format('MMM D, YYYY')}`
    case 'month': return currentDate.value.format('MMMM YYYY')
  }
})

const goToToday = () => currentDate.value = dayjs()
const goToPrev = () => {
  if (view.value === 'day') currentDate.value = currentDate.value.subtract(1, 'day')
  else if (view.value === 'week') currentDate.value = currentDate.value.subtract(1, 'week')
  else currentDate.value = currentDate.value.subtract(1, 'month')
}
const goToNext = () => {
  if (view.value === 'day') currentDate.value = currentDate.value.add(1, 'day')
  else if (view.value === 'week') currentDate.value = currentDate.value.add(1, 'week')
  else currentDate.value = currentDate.value.add(1, 'month')
}

const monthCells = computed(() => {
  const start = currentDate.value.startOf('month').startOf('week')
  const end = currentDate.value.endOf('month').endOf('week')
  const days = []
  let day = start

  while (day.isBefore(end) || day.isSame(end)) {
    days.push({ date: day, currentMonth: day.month() === currentDate.value.month() })
    day = day.add(1, 'day')
  }

  return days
})

const weekDays = computed(() => {
  const start = currentDate.value.startOf('week')
  return Array.from({ length: 7 }, (_, i) => start.add(i, 'day'))
})


const events = ref([
  {
    appointment_with: "Lead",
    calendar_event: null,
    creation: "2025-05-08 03:54:12.163175",
    customer_details: null,
    customer_email: "muqeetmughal786@gmail.com",
    customer_name: "Meeting with muqeet",
    customer_phone_number: null,
    customer_skype: null,
    docstatus: 0,
    idx: 0,
    modified: "2025-05-08 03:54:12.163175",
    modified_by: "Administrator",
    name: "APMT-Meeting with muqeet-0001",
    owner: "Administrator",
    party: null,
    scheduled_time: "2025-05-08 03:53:51",
    status: "Open"
  }
])

</script>

<style scoped>
/* Optional custom styles */
</style>
