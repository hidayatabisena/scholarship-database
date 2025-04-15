<script setup lang="ts">
import { ref, watch } from 'vue';
import { PlusCircleIcon } from '@heroicons/vue/24/outline';
import AnimatedSearchBar from './AnimatedSearchBar.vue';
import TallyFormModal from './TallyFormModal.vue';

const searchQuery = ref('');
const emit = defineEmits(['search']);
const isModalOpen = ref(false);

watch(searchQuery, (newValue) => {
  emit('search', newValue);
});

const refreshPage = () => {
  window.location.href = '/';
};

const openModal = () => {
  isModalOpen.value = true;
};

const closeModal = () => {
  isModalOpen.value = false;
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
        
        <div class="flex items-center space-x-4">
          <button
            @click="openModal"
            class="p-2 rounded-full hover:bg-gray-100 dark:hover:bg-gray-700 transition-colors"
            aria-label="Submit scholarship"
            title="Submit scholarship"
          >
            <PlusCircleIcon class="h-6 w-6 text-gray-600 dark:text-gray-300" />
          </button>
          <AnimatedSearchBar v-model="searchQuery" />
        </div>
      </div>
    </div>
  </nav>
  
  <!-- Tally Form -->
  <TallyFormModal 
    :is-open="isModalOpen" 
    :tally-form-url="'https://tally.so/r/3x6zNk'"
    @close="closeModal"
  />
</template>