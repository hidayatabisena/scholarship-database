<script setup lang="ts">
import { computed, ref, onMounted } from 'vue';
import { useWindowSize } from '@vueuse/core';
import type { Scholarship } from '../types';
import scholarshipIndex from '../data/scholarships/index.json';
import ScholarshipCard from './ScholarshipCard.vue';
import CategoryFilter from './CategoryFilter.vue';

const props = defineProps<{
  searchQuery: string
}>();

const scholarships = ref<Record<string, Scholarship[]>>({});
const loading = ref(true);
const selectedCategory = ref<string | null>(null);

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

  const dataToFilter = selectedCategory.value 
    ? { [selectedCategory.value]: scholarships.value[selectedCategory.value] } 
    : scholarships.value;
    
  if (!query) {
    filtered = JSON.parse(JSON.stringify(dataToFilter));
  } else {
    Object.entries(dataToFilter).forEach(([category, items]) => {
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

const categoryList = computed(() => {
  return Object.keys(scholarships.value);
});

// const isMobile = computed(() => width.value < 768);
</script>

<template>
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex items-center justify-end gap-3 mb-6">
      <!-- Category Dropdown -->
      <CategoryFilter 
        :categories="categoryList" 
        :selectedCategory="selectedCategory"
        @update:selectedCategory="selectedCategory = $event"
        class="w-48"
      />
      
      <!-- Sort Button -->
      <button 
        @click="toggleSortOrder" 
        class="px-3 py-2 text-sm bg-gray-100 dark:bg-gray-800 rounded-md hover:bg-gray-200 dark:hover:bg-gray-700 transition-colors"
      >
        Sort {{ sortOrder === 'asc' ? '↓' : '↑' }}
      </button>
    </div>
    
    <div v-if="loading" class="flex justify-center items-center py-12">
      <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-gray-900 dark:border-gray-100"></div>
    </div>
    
    <div v-else-if="Object.keys(filteredScholarships).length === 0" class="text-center py-12">
      <p class="text-gray-600 dark:text-gray-400">No scholarships found matching your criteria.</p>
    </div>
    
    <div v-else class="space-y-8">
      <template v-for="category in sortedCategories" :key="category">
        <section v-if="filteredScholarships[category].length > 0">
          <h2 class="text-xl font-semibold mb-4 text-[#37352f] dark:text-[#fbfbfa]">{{ category }}</h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-3">
            <ScholarshipCard
              v-for="scholarship in filteredScholarships[category]"
              :key="scholarship.id"
              :scholarship="scholarship"
            />
          </div>
        </section>
      </template>
    </div>
  </div>
</template>