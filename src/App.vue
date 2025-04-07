<script setup lang="ts">
import { ref } from 'vue';
import Navigation from './components/Navigation.vue';
import ScholarshipList from './components/ScholarshipList.vue';

const searchQuery = ref('');

const updateSearchQuery = (query: string) => {
  searchQuery.value = query;
};
</script>

<template>
  <div class="min-h-screen bg-[#f7f6f3] dark:bg-[#1f1f1f] text-[#37352f] dark:text-[#fbfbfa]">
    <Navigation @search="updateSearchQuery" />
    
    <main class="py-8">
      <ScholarshipList :searchQuery="searchQuery" />
    </main>
  </div>
</template>
