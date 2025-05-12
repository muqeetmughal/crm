<template>
    <Dialog v-model="show" :options="{ size: '3xl' }">
      <template #body>
        <div class="bg-surface-modal px-4 pb-6 pt-5 sm:px-6">
          <div class="mb-5 flex items-center justify-between">
            <div>
              <h3 class="text-2xl font-semibold leading-6 text-ink-gray-9">
                {{ __('Create Appointment') }}
              </h3>
            </div>
            <div class="flex items-center gap-1">
              <Button
                v-if="isManager() && !isMobileView"
                variant="ghost"
                class="w-7"
                @click="openQuickEntryModal"
              >
                <EditIcon class="h-4 w-4" />
              </Button>
              <Button variant="ghost" class="w-7" @click="show = false">
                <FeatherIcon name="x" class="h-4 w-4" />
              </Button>
            </div>
          </div>
          <div>
            <FieldLayout v-if="tabs.data" :tabs="tabs.data" :data="appointment" />
            <ErrorMessage class="mt-4" v-if="error" :message="__(error)" />
          </div>
        </div>
        <div class="px-4 pb-7 pt-4 sm:px-6">
          <div class="flex flex-row-reverse gap-2">
            <Button
              variant="solid"
              :label="__('Create')"
              :loading="isAppointmentCreating"
              @click="createNewAppointment"
            />
          </div>
        </div>
      </template>
    </Dialog>
  </template>

  <script setup>
  import EditIcon from '@/components/Icons/EditIcon.vue'
  import FieldLayout from '@/components/FieldLayout/FieldLayout.vue'
  import { usersStore } from '@/stores/users'
  import { statusesStore } from '@/stores/statuses'
  import { isMobileView } from '@/composables/settings'
  import { capture } from '@/telemetry'
  import { createResource } from 'frappe-ui'
  import { useOnboarding } from 'frappe-ui/frappe'
  import { computed, onMounted, ref, reactive, nextTick } from 'vue'
  import { useRouter } from 'vue-router'

  const props = defineProps({
    defaults: Object,
  })

  const { getUser, isManager } = usersStore()
  const {  statusOptions } = statusesStore()
  const { updateOnboardingStep } = useOnboarding('frappecrm')

  const show = defineModel()
  const router = useRouter()
  const error = ref(null)
  const isAppointmentCreating = ref(false)

  const tabs = createResource({
    url: 'crm.fcrm.doctype.crm_fields_layout.crm_fields_layout.get_fields_layout',
    cache: ['QuickEntry', 'Appointment'],
    params: { doctype: 'Appointment', type: 'Quick Entry' },
    auto: true,
    transform: (_tabs) => {
      return _tabs.forEach((tab) => {
        tab.sections.forEach((section) => {
          section.columns.forEach((column) => {
            column.fields.forEach((field) => {

              if (field.fieldtype === 'Table') {
                appointment[field.fieldname] = []
              }
            })
          })
        })
      })
    },
  })

  const appointment = reactive({
    appointment_with: '',
    calendar_event: '',
    customer_details: null,
    customer_email: '',
    customer_name: '',
    customer_phone_number: null,
    customer_skype: null,
    party: null,
    scheduled_time: '',
    status: 'Open',
  })

  const createAppointment = createResource({
    url: 'frappe.client.insert',
    makeParams(values) {
      return {
        doc: {
          doctype: 'Appointment',
          ...values,
        },
      }
    },
  })



  function createNewAppointment() {
    if (appointment.website && !appointment.website.startsWith('http')) {
      appointment.website = 'https://' + appointment.website
    }

    // createLead.submit(appointment, {
    //   validate() {
    //     error.value = null
    //     if (!appointment.first_name) {
    //       error.value = __('First Name is mandatory')
    //       return error.value
    //     }
    //     if (appointment.annual_revenue) {
    //       if (typeof appointment.annual_revenue === 'string') {
    //         appointment.annual_revenue = appointment.annual_revenue.replace(/,/g, '')
    //       } else if (isNaN(appointment.annual_revenue)) {
    //         error.value = __('Annual Revenue should be a number')
    //         return error.value
    //       }
    //     }
    //     if (appointment.mobile_no && isNaN(appointment.mobile_no.replace(/[-+() ]/g, ''))) {
    //       error.value = __('Mobile No should be a number')
    //       return error.value
    //     }
    //     if (appointment.email && !appointment.email.includes('@')) {
    //       error.value = __('Invalid Email')
    //       return error.value
    //     }
    //     if (!appointment.status) {
    //       error.value = __('Status is required')
    //       return error.value
    //     }
    //     isAppointmentCreating.value = true
    //   },
    //   onSuccess(data) {
    //     capture('lead_created')
    //     isAppointmentCreating.value = false
    //     show.value = false
    //     router.push({ name: 'Lead', params: { leadId: data.name } })
    //     updateOnboardingStep('create_first_lead', true, false, () => {
    //       localStorage.setItem('firstLead', data.name)
    //     })
    //   },
    //   onError(err) {
    //     isAppointmentCreating.value = false
    //     if (!err.messages) {
    //       error.value = err.message
    //       return
    //     }
    //     error.value = err.messages.join('\n')
    //   },
    // })
  }

  const showQuickEntryModal = defineModel('quickEntry')

  function openQuickEntryModal() {
    showQuickEntryModal.value = true
    nextTick(() => {
      show.value = false
    })
  }

  onMounted(() => {
    // Object.assign(appointment, props.defaults)
    // if (!appointment.lead_owner) {
    //   appointment.lead_owner = getUser().name
    // }
    // if (!appointment.status && leadStatuses.value[0]?.value) {
    //   appointment.status = leadStatuses.value[0].value
    // }
  })
  </script>
