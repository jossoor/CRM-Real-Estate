<template>
  <div
    class="h-full w-60 bg-white border-r flex flex-col"
  >
    <!-- Logo -->
    <div class="h-14 flex items-center px-4 border-b text-lg font-semibold">
      Logo
    </div>

    <!-- Menu -->
    <nav class="flex-1 px-2 py-3 space-y-1 text-sm">

    <!-- Dashboard -->
    <SidebarLink
      label="Dashboard"
      icon="dashboard"
      :to="{ name: 'Dashboard' }"
      :class="linkClass('Dashboard')"
    />

    <!-- Leads -->
    <div class="relative">
      <SidebarLink
        label="Leads"
        icon="users"
        :to="{ name: 'Leads' }"
        :class="linkClass('Leads')"
      />

      <button
        type="button"
        class="absolute right-2 top-1/2 -translate-y-1/2 p-1 rounded hover:bg-gray-200 z-10"
        @click.stop.prevent="toggle('leads')"
      >
        <Chevron :open="open.leads" />
      </button>

      <div
        v-show="open.leads"
        class="ml-6 mt-1 space-y-1 border-l border-gray-200 pl-3"
      >
        <SubLink label="Create Lead" @click="createLead" />
        <SubLink
          v-for="status in leadStatuses"
          :key="status"
          :label="status"
          @click="filterLead(status)"
        />
      </div>
    </div>

    <!-- Inventory -->
    <div class="relative">
      <SidebarLink
        label="Inventory"
        icon="box"
        :to="{ name: 'Inventory' }"
        :class="linkClass('Inventory')"
      />

      <button
        type="button"
        class="absolute right-2 top-1/2 -translate-y-1/2 p-1 rounded hover:bg-gray-200 z-10"
        @click.stop.prevent="toggle('inventory')"
      >
        <Chevron :open="open.inventory" />
      </button>

      <div
        v-show="open.inventory"
        class="ml-6 mt-1 space-y-1 border-l border-gray-200 pl-3"
      >
        <SubLink
          label="Projects"
          :to="{ name: 'Inventory', query: { tab: 'projects' } }"
        />
        <SubLink
          label="Units"
          :to="{ name: 'Inventory', query: { tab: 'units' } }"
        />
      </div>
    </div>

    <!-- Reservations -->
    <SidebarLink
      label="Reservations"
      icon="calendar"
      :to="{ name: 'Reservations' }"
      :class="linkClass('Reservations')"
    />

    <!-- Deals -->
    <SidebarLink
      label="Deals"
      icon="briefcase"
      :to="{ name: 'Deals' }"
      :class="linkClass('Deals')"
    />

    <!-- Tasks -->
    <SidebarLink
      label="Tasks"
      icon="check"
      :to="{ name: 'Tasks' }"
      :class="linkClass('Tasks')"
    />

    <!-- Notes -->
    <SidebarLink
      label="Notes"
      icon="edit"
      :to="{ name: 'Notes' }"
      :class="linkClass('Notes')"
    />

  </nav>

    <!-- Footer -->
    <div class="border-t p-3 text-xs text-gray-400">
      CRM Portal
    </div>

    <Notifications />
    <Settings />
  </div>
</template>

<script setup>
import { reactive } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useStorage } from '@vueuse/core'

import SidebarLink from '@/components/SidebarLink.vue'
import Notifications from '@/components/Notifications.vue'
import Settings from '@/components/Settings/Settings.vue'

const route = useRoute()
const router = useRouter()
const isSidebarCollapsed = useStorage('isSidebarCollapsed', false)

const open = reactive({
  leads: false,
  inventory: false,
})

if (route.name === 'Leads') {
  open.leads = true
}

if (route.name === 'Inventory') {
  open.inventory = true
}

const leadStatuses = [
  'New',
  'Hot',
  'Qualified',
  'Follow Up',
  'Meeting',
  'No Answer',
  'Unqualified',
]

function toggle(key) {
  open[key] = !open[key]
}

function linkClass(name) {
  return route.name === name
    ? 'bg-blue-100 text-blue-700 font-medium'
    : 'text-gray-700 hover:bg-gray-100'
}

function createLead() {
  router.push({
    name: 'Leads',
    query: { create: '1', _t: Date.now() },
  })
}

function filterLead(status) {
  router.push({
    name: 'Leads',
    query: { status, _t: Date.now() },
  })
}
</script>

<script>
export const Chevron = {
  props: ['open'],
  template: `
    <svg
      class="w-4 h-4 transition-transform"
      :class="open ? 'rotate-90' : ''"
      fill="none"
      stroke="currentColor"
      viewBox="0 0 24 24"
    >
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
        d="M9 5l7 7-7 7" />
    </svg>
  `,
}

export const SubLink = {
  props: ['label', 'to'],
  emits: ['click'],
  template: `
    <button
      v-if="!to"
      @click="$emit('click')"
      class="block w-full text-left text-sm px-3 py-1 rounded hover:bg-gray-100 text-gray-600"
    >
      {{ label }}
    </button>

    <router-link
      v-else
      :to="to"
      class="block text-sm px-3 py-1 rounded hover:bg-gray-100 text-gray-600"
    >
      {{ label }}
    </router-link>
  `,
}
</script>
