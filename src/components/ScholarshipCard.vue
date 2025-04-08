<script setup lang="ts">
import { ArrowTopRightOnSquareIcon } from '@heroicons/vue/24/outline';
import type { Scholarship } from '../types';

defineProps<{
  scholarship: Scholarship
}>();

const getHostname = (url: string) => {
  try {
    return new URL(url).hostname;
  } catch {
    return url;
  }
};
</script>

<template>
  <div class="notion-card group relative bg-white dark:bg-gray-800 p-4 rounded-lg shadow-sm hover:shadow-md transition-shadow">
    <div class="flex flex-col h-full">
      <div class="flex-1 min-w-0">
        <h3 class="text-[#37352f] dark:text-[#fbfbfa] text-base font-medium leading-6 mb-1 pr-8">
          {{ scholarship.description }}
        </h3>
        <p class="text-sm text-[#787774] dark:text-[#999999] truncate">
          {{ getHostname(scholarship.link) }}
        </p>
      </div>
      <a
        :href="scholarship.link"
        target="_blank"
        rel="noopener noreferrer"
        class="absolute top-3 right-3 p-1.5 rounded-md notion-button opacity-70 group-hover:opacity-100 transition-opacity duration-200"
      >
        <ArrowTopRightOnSquareIcon class="w-4 h-4 text-gray-700 dark:text-gray-300" />
      </a>
    </div>
  </div>
</template>

<style scoped>
.notion-card {
  border: 1px solid rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
}

.dark .notion-card {
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.notion-card:hover {
  background-color: rgba(0, 0, 0, 0.02);
}

.dark .notion-card:hover {
  background-color: rgba(255, 255, 255, 0.05);
}

.notion-button {
  background-color: rgba(0, 0, 0, 0.08);
}

.dark .notion-button {
  background-color: rgba(255, 255, 255, 0.15);
}

.notion-button:hover {
  background-color: rgba(0, 0, 0, 0.15);
}

.dark .notion-button:hover {
  background-color: rgba(255, 255, 255, 0.25);
}
</style>