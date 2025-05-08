<template>
  <LayoutHeader>
    <template #left-header>
      <ViewBreadcrumbs v-model="viewControls" routeName="Appointments" />
    </template>
    <template #right-header>
      <CustomActions v-if="leadsListView?.customListActions" :actions="leadsListView.customListActions" />
      <Button variant="solid" :label="__('Create')" @click="showLeadModal = true">
        <template #prefix>
          <FeatherIcon name="plus" class="h-4" />
        </template>
      </Button>
    </template>
  </LayoutHeader>
  <ViewControls ref="viewControls" v-model="appointments" v-model:loadMore="loadMore"
    v-model:resizeColumn="triggerResize" v-model:updatedPageCount="updatedPageCount"
     doctype="Appointment"
     
    :options="{
      allowedViews: ['list', 'group_by', 'kanban', 'calendar'],
      hideColumnsButton : true,
    }"
    />
  <CalendarView />
</template>

<script setup>
import ViewBreadcrumbs from '@/components/ViewBreadcrumbs.vue'
import MultipleAvatar from '@/components/MultipleAvatar.vue'
import CustomActions from '@/components/CustomActions.vue'
import EmailAtIcon from '@/components/Icons/EmailAtIcon.vue'
import PhoneIcon from '@/components/Icons/PhoneIcon.vue'
import NoteIcon from '@/components/Icons/NoteIcon.vue'
import TaskIcon from '@/components/Icons/TaskIcon.vue'
import CommentIcon from '@/components/Icons/CommentIcon.vue'
import IndicatorIcon from '@/components/Icons/IndicatorIcon.vue'
import LeadsIcon from '@/components/Icons/LeadsIcon.vue'
import LayoutHeader from '@/components/LayoutHeader.vue'
import LeadsListView from '@/components/ListViews/LeadsListView.vue'
import CalendarView from '@/components/Calendar/CalendarView.vue'
import LeadModal from '@/components/Modals/LeadModal.vue'
import NoteModal from '@/components/Modals/NoteModal.vue'
import TaskModal from '@/components/Modals/TaskModal.vue'
import QuickEntryModal from '@/components/Modals/QuickEntryModal.vue'
import ViewControls from '@/components/ViewControls.vue'
import { getMeta } from '@/stores/meta'
import { globalStore } from '@/stores/global'
import { usersStore } from '@/stores/users'
import { statusesStore } from '@/stores/statuses'
import { callEnabled } from '@/composables/settings'
import { formatDate, timeAgo, website, formatTime } from '@/utils'
import { Avatar, Tooltip, Dropdown } from 'frappe-ui'
import { useRoute } from 'vue-router'
import { ref, computed, reactive, h } from 'vue'

const { getFormattedPercent, getFormattedFloat, getFormattedCurrency } =
  getMeta('CRM Lead')
const { getUser } = usersStore()
const { getLeadStatus } = statusesStore()

const route = useRoute()

const leadsListView = ref(null)
const showLeadModal = ref(false)
const showQuickEntryModal = ref(false)

const defaults = reactive({})

// leads data is loaded in the ViewControls component
const appointments = ref({})
const loadMore = ref(1)
const triggerResize = ref(1)
const updatedPageCount = ref(20)
const viewControls = ref(null)






</script>