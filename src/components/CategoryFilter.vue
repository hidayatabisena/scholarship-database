<script setup lang="ts">
import { ChevronDownIcon } from '@heroicons/vue/24/outline';

defineProps<{
  categories: string[]
  selectedCategory: string | null
}>();

const emit = defineEmits<{
  (e: 'update:selectedCategory', value: string | null): void
}>();

const updateCategory = (event: Event) => {
  const value = (event.target as HTMLSelectElement).value;
  emit('update:selectedCategory', value === 'all' ? null : value);
};
</script>

<template>
  <div class="relative">
    <select
      :value="selectedCategory === null ? 'all' : selectedCategory"
      @change="updateCategory"
      class="appearance-none block w-full pl-3 pr-10 py-2 text-sm border-gray-300 dark:border-gray-700 rounded-md bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
    >
      <option value="all">All Categories</option>
      <option v-for="category in categories" :key="category" :value="category">
        {{ category }}
      </option>
    </select>
    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700 dark:text-gray-300">
      <ChevronDownIcon class="h-4 w-4" />
    </div>
  </div>
</template>