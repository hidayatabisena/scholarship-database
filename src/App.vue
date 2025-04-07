<script setup lang="ts">
import ScholarshipList from './components/ScholarshipList.vue'
import { ref } from 'vue'

const searchQuery = ref('');
</script>

<template>
  <div class="min-h-screen bg-[#fbfbfa] dark:bg-[#191919]">
    <header class="sticky top-0 z-10 bg-[#fbfbfa]/80 dark:bg-[#191919]/80 backdrop-blur-sm border-b border-[#e6e6e6] dark:border-[#363636]">
      <div class="max-w-7xl mx-auto py-4 px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between">
          <h1 class="text-2xl font-bold tracking-tight">
            Scholarship Database
          </h1>
          <div class="w-72">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Search scholarships..."
              class="w-full px-3 py-2 rounded-lg notion-search text-[#37352f] dark:text-[#fbfbfa] text-sm"
            />
          </div>
        </div>
      </div>
    </header>
    <main class="py-6">
      <ScholarshipList :search-query="searchQuery" />
    </main>
  </div>
</template>
