<template>
  <div class="w-64 h-full bg-gray-900 shadow-md p-4 space-y-2 text-white overflow-y-auto">
    <div v-for="(item, index) in links" :key="index" class="space-y-1">
      <!-- Menu cha có children -->
      <button v-if="item.children" @click="toggleMenu(item.path)"
        class="w-full flex items-center justify-between px-4 py-2 rounded-lg hover:bg-gray-700 transition-all duration-200"
        :class="isParentRoute(item.path) ? 'bg-blue-600 text-white' : ''">
        <div class="flex items-center space-x-3 truncate">
          <component :is="item.icon" class="w-5 h-5"
            :class="isParentRoute(item.path) ? 'text-white' : 'text-gray-400'" />
          <span class="font-medium truncate" :title="item.label">{{ item.label }}</span>
        </div>
        <svg :class="openMenus.includes(item.path) ? 'rotate-90' : ''"
          class="w-4 h-4 transform transition-transform duration-200" fill="none" stroke="currentColor" stroke-width="2"
          viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
        </svg>
      </button>

      <!-- Menu không có children -->
      <router-link v-else :to="item.path"
        class="flex items-center space-x-3 px-4 py-2 rounded-lg hover:bg-gray-700 transition-all duration-200"
        :class="isActiveRoute(item.path) ? 'bg-blue-700 font-semibold shadow-inner' : 'text-gray-300'">
        <component :is="item.icon" class="w-5 h-5" :class="isActiveRoute(item.path) ? 'text-white' : 'text-gray-400'" />
        <span class="font-medium truncate" :title="item.label">{{ item.label }}</span>
      </router-link>

      <!-- Submenu -->
      <Transition name="submenu">
        <div v-if="item.children && openMenus.includes(item.path)" class="pl-8 space-y-1 overflow-hidden">
          <router-link v-for="(child, cIndex) in item.children" :key="cIndex" :to="child.path"
            class="flex items-center space-x-2 px-3 py-1 rounded-lg text-sm hover:bg-gray-700 transition-all duration-200"
            :class="isActiveRoute(child.path)
              ? 'bg-blue-500 font-medium border-l-4 border-white text-white'
              : 'text-gray-300'">
            <component :is="child.icon" class="w-4 h-4" />
            <span class="truncate" :title="child.label">{{ child.label }}</span>
          </router-link>
        </div>
      </Transition>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'
import { HomeIcon, UserIcon, Cog6ToothIcon, ArrowLeftOnRectangleIcon } from '@heroicons/vue/24/outline'

const route = useRoute()

const links = [
  { label: 'Home', path: '/', icon: HomeIcon },
  { label: 'Profile', path: '/profile', icon: UserIcon },
  {
    label: 'Settings',
    path: '/settings',
    icon: Cog6ToothIcon,
    children: [
      { label: 'General', path: '/settings/general', icon: Cog6ToothIcon },
      { label: 'Privacy', path: '/settings/privacy', icon: Cog6ToothIcon },
    ],
  },
  { label: 'Logout', path: '/logout', icon: ArrowLeftOnRectangleIcon },
]

const openMenus = ref([])

const toggleMenu = (path) => {
  openMenus.value = openMenus.value.includes(path)
    ? openMenus.value.filter((p) => p !== path)
    : [...openMenus.value, path]
}

const isActiveRoute = (path) => route.path === path
const isParentRoute = (parentPath) => route.path.startsWith(parentPath)

watch(
  () => route.path,
  (newPath) => {
    links.forEach((item) => {
      if (
        item.children &&
        newPath.startsWith(item.path) &&
        !openMenus.value.includes(item.path)
      ) {
        openMenus.value.push(item.path)
      }
    })
  },
  { immediate: true }
)
</script>

<style scoped>
/* Slide-down animation for submenu */
.submenu-enter-active,
.submenu-leave-active {
  transition: max-height 0.3s ease, opacity 0.3s ease;
}

.submenu-enter-from,
.submenu-leave-to {
  max-height: 0;
  opacity: 0;
}

.submenu-enter-to,
.submenu-leave-from {
  max-height: 500px;
  opacity: 1;
}
</style>
