<script setup lang="ts">
import { ref, watch } from 'vue';
import { MagnifyingGlassIcon, XMarkIcon } from '@heroicons/vue/24/outline';

const props = defineProps<{
  modelValue: string;
}>();

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void;
}>();

const isExpanded = ref(false);
const searchInput = ref<HTMLInputElement | null>(null);
const searchQuery = ref(props.modelValue);

watch(() => props.modelValue, (newValue) => {
  searchQuery.value = newValue;
});

watch(searchQuery, (newValue) => {
  emit('update:modelValue', newValue);
});

const toggleSearch = () => {
  isExpanded.value = !isExpanded.value;
  
  if (isExpanded.value) {
    setTimeout(() => {
      searchInput.value?.focus();
    }, 300);
  } else {
    if (searchQuery.value) {
      searchQuery.value = '';
    }
  }
};

const handleKeyDown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    toggleSearch();
  }
};
</script>

<template>
  <div class="relative flex items-center">
    <div 
      class="flex items-center transition-all duration-300 ease-in-out"
      :class="[
        isExpanded ? 'w-64 bg-white dark:bg-gray-800 rounded-md shadow-sm' : 'w-10',
      ]"
    >
      <button 
        @click="toggleSearch" 
        class="flex items-center justify-center h-10 w-10 rounded-md hover:bg-gray-100 dark:hover:bg-gray-700 transition-colors"
        :aria-label="isExpanded ? 'Close search' : 'Open search'"
      >
        <MagnifyingGlassIcon v-if="!isExpanded" class="h-5 w-5 text-gray-600 dark:text-gray-300" />
        <XMarkIcon v-else class="h-5 w-5 text-gray-600 dark:text-gray-300" />
      </button>
      
      <input
        v-show="isExpanded"
        ref="searchInput"
        v-model="searchQuery"
        type="text"
        placeholder="Search scholarships..."
        class="w-full bg-transparent border-none focus:ring-0 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 px-2 py-2"
        @keydown="handleKeyDown"
      />
    </div>
  </div>
</template>