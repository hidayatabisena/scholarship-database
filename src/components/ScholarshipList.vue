<script setup lang="ts">
import { computed, ref, onMounted } from 'vue';
import { useWindowSize } from '@vueuse/core';
import { ArrowTopRightOnSquareIcon } from '@heroicons/vue/24/outline';
import type { Scholarship } from '../types';
import scholarshipIndex from '../data/scholarships/index.json';

const props = defineProps<{
  searchQuery: string
}>();

const { width } = useWindowSize();

const scholarships = ref<Record<string, Scholarship[]>>({});
const loading = ref(true);

onMounted(async () => {
  try {
    const scholarshipData: Record<string, Scholarship[]> = {};
    
    for (const category of scholarshipIndex.categories) {
      const response = await fetch(`/src/data/scholarships/${category.file}`);
      const data = await response.json();
      
      const categoryName = Object.keys(data)[0];
      scholarshipData[categoryName] = data[categoryName];
    }
    
    scholarships.value = scholarshipData;
  } catch (error) {
    console.error('Error loading scholarship data:', error);
  } finally {
    loading.value = false;
  }
});

const sortOrder = ref<'asc' | 'desc'>('asc');

const toggleSortOrder = () => {
  sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
};

const filteredScholarships = computed(() => {
  const query = props.searchQuery.toLowerCase();
  let filtered: Record<string, Scholarship[]> = {};

  if (!query) {
    filtered = JSON.parse(JSON.stringify(scholarships.value));
  } else {
    Object.entries(scholarships.value).forEach(([category, items]) => {
      const matchingItems = items.filter(scholarship => 
        scholarship.description.toLowerCase().includes(query) ||
        scholarship.link.toLowerCase().includes(query)
      );
      
      if (matchingItems.length > 0) {
        filtered[category] = matchingItems;
      }
    });
  }
  
  return filtered;
});

const sortedCategories = computed(() => {
  const categories = Object.keys(filteredScholarships.value);
  
  return categories.sort((a, b) => {
    const compareResult = a.localeCompare(b);
    return sortOrder.value === 'asc' ? compareResult : -compareResult;
  });
});

const getHostname = (url: string) => {
  try {
    return new URL(url).hostname;
  } catch {
    return url;
  }
};

const isMobile = computed(() => width.value < 768);
</script>

<template>
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex justify-end mb-4">
      <button 
        @click="toggleSortOrder" 
        class="px-3 py-1 text-sm bg-gray-100 dark:bg-gray-800 rounded-md hover:bg-gray-200 dark:hover:bg-gray-700 transition-colors"
      >
        Sort Categories {{ sortOrder === 'asc' ? '↓' : '↑' }}
      </button>
    </div>
    
    <div v-if="loading" class="flex justify-center items-center py-12">
      <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-gray-900 dark:border-gray-100"></div>
    </div>
    
    <div v-else class="space-y-8">
      <template v-for="category in sortedCategories" :key="category">
        <section v-if="filteredScholarships[category].length > 0">
          <h2 class="text-xl font-semibold mb-4 text-[#37352f] dark:text-[#fbfbfa]">{{ category }}</h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-3">
            <div
              v-for="scholarship in filteredScholarships[category]"
              :key="scholarship.id"
              class="notion-card group relative"
            >
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
                  class="absolute top-3 right-3 p-1.5 rounded-md notion-button opacity-0 group-hover:opacity-100 transition-opacity duration-200"
                >
                  <ArrowTopRightOnSquareIcon class="w-4 h-4" />
                </a>
              </div>
            </div>
          </div>
        </section>
      </template>
    </div>
  </div>
</template>