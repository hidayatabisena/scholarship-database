<script setup lang="ts">
import { ref, watch } from 'vue';
import AnimatedSearchBar from './AnimatedSearchBar.vue';

const searchQuery = ref('');
const emit = defineEmits(['search']);

watch(searchQuery, (newValue) => {
  emit('search', newValue);
});

const refreshPage = () => {
  window.location.href = '/';
};
</script>

<template>
  <nav class="bg-white dark:bg-gray-800 shadow-sm">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between h-16">
        <div class="flex items-center">
          <div class="flex-shrink-0">
            <a 
              href="/"
              @click.prevent="refreshPage"
              class="text-xl font-bold text-gray-900 dark:text-white hover:text-blue-600 dark:hover:text-blue-400 transition-colors cursor-pointer"
            >
              NextStopScholar
            </a>
          </div>
        </div>
        
        <div class="flex items-center">
          <AnimatedSearchBar v-model="searchQuery" />
        </div>
      </div>
    </div>
  </nav>
</template>