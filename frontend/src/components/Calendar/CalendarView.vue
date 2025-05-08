<template>
    <div class="flex h-screen flex-col overflow-hidden p-5">
    <Calendar
      :config="{
        defaultMode: 'Month',
        isEditMode: true,
        eventIcons: {
        },
        allowCustomClickEvents: true,
        redundantCellHeight: 100,
        enableShortcuts: false,
      }"
      :events="[
        {
          title: 'English by Ryan Mathew',
          participant: 'Ryan Mathew',
          id: 'EDU-CSH-2024-00091',
          venue: 'CNF-ROOM-2024-00001',
          fromDate: '2025-05-02 16:30:00',
          toDate: '2025-05-02 17:30:00',
          color: 'violet',
        },
        {
          title: 'English by Ryan Mathew',
          participant: 'Ryan Mathew',
          id: 'EDU-CSH-2024-00092',
          venue: 'CNF-ROOM-2024-00002',
          fromDate: '2025-05-04 13:30:00',
          toDate: '2025-05-04 17:30:00',
          color: 'green',
        },
        {
          title: 'English by Sheldon',
          participant: 'Sheldon',
          id: 'EDU-CSH-2024-00093',
          venue: 'CNF-ROOM-2024-00001',
          fromDate: '2025-05-16 10:30:00',
          toDate: '2025-05-16 11:30:00',
          color: 'blue',
        },
        {
          title: 'English by Ryan Mathew',
          participant: 'Ryan Mathew',
          id: 'EDU-CSH-2024-00094',
          venue: 'CNF-ROOM-2024-00001',
          fromDate: '2025-05-21 16:30:00',
          toDate: '2025-05-21 17:30:00',
          color: 'red',
        },
        {
          title: 'Google Meet with John ',
          participant: 'John',
          id: '#htrht41',
          venue: 'Google Meet',
          fromDate: '2025-05-11 00:00:00',
          toDate: '2025-05-11 23:59:59',
          color: 'amber',
          isFullDay: true,
        },
        {
          title: 'Zoom Meet with Sheldon',
          participant: 'Sheldon',
          id: '#htrht42',
          venue: 'Google Meet',
          fromDate: '2025-05-07 00:00:00',
          toDate: '2025-05-07 23:59:59',
          color: 'amber',
          isFullDay: true,
        },
      ]"
      @create="(event) => logEvent('createEvent', event)"
      @update="(event) => logEvent('updateEvent', event)"
      @delete="(eventID) => logEvent('deleteEvent', eventID)"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { Calendar } from 'frappe-ui'
import { formatDate, formatTime } from '@/utils'
import { useRoute } from 'vue-router'


const route = useRoute()

</script>