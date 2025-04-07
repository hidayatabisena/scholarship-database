<script setup lang="ts">
import { computed } from 'vue';
import { useWindowSize } from '@vueuse/core';
import { ArrowTopRightOnSquareIcon } from '@heroicons/vue/24/outline';
import type { Scholarship } from '../types';

const props = defineProps<{
  searchQuery: string
}>();

const { width } = useWindowSize();

const scholarships: Record<string, Scholarship[]> = {
  'Study in US': [
    { id: 1, link: 'https://www.oregoncf.org/grants-and-scholarships/scholarships/', description: 'Oregon Community Foundation scholarship opportunities' },
    { id: 2, link: 'https://www.collegefund.org/students/scholarships/', description: 'American Indian College Fund scholarships' },
    { id: 3, link: 'https://www.udall.gov/', description: 'Udall Foundation scholarships for Native American students' },
    { id: 4, link: 'https://utulsa.academicworks.com/', description: 'University of Tulsa\'s internal scholarship portal' },
    { id: 8, link: 'https://www.bie.edu/Parents-Students/Grants', description: 'Bureau of Indian Education grants for students' },
    { id: 9, link: 'https://www.haskell.edu/financial-aid/scholarships/', description: 'Haskell Indian Nations University scholarships' },
    { id: 11, link: 'https://www.aigcs.org/scholarships-fellowships/graduate-students/', description: 'American Indian Graduate Center scholarships for grad students' },
    { id: 12, link: 'https://www.aigcs.org/scholarships-fellowships/undergraduate-students/', description: 'AIGC scholarships for undergraduate students' },
    { id: 18, link: 'https://dar.academicworks.com/opportunities/1073', description: 'Daughters of the American Revolution scholarship for Native Americans' }
  ],
  'Tribal Nation Scholarships': [
    { id: 5, link: 'https://mytribe.cherokee.org/', description: 'Cherokee Nation citizen services portal' },
    { id: 6, link: 'https://www.cherokee.org/all-services/education-services/higher-education/', description: 'Cherokee Nation Higher Education services' },
    { id: 7, link: 'https://cobellscholar.academicworks.com/', description: 'Cobell Scholarship for Native American students' },
    { id: 10, link: 'https://www.tmfoundation.org/scholarships.html', description: 'Tyonek Native Corporation scholarship opportunities' }
  ],
  'Organizations & Foundations': [
    { id: 13, link: 'https://www.aigcs.org/scholarships-fellowships/additional-scholarships/', description: 'Additional scholarships from AIGC' },
    { id: 15, link: 'https://americanindianservices.org/students/', description: 'AIS scholarships for Native American students' },
    { id: 16, link: 'https://www.catchingthedream.org/application-reqs', description: 'Catching the Dream scholarship info and requirements' },
    { id: 17, link: 'https://www.indian-affairs.org/scholarships.html', description: 'Association on American Indian Affairs scholarship listings' },
    { id: 19, link: 'https://nativeforward.org/scholarships/undergraduate-scholarships/', description: 'Undergraduate scholarships from Native Forward Scholars Fund' }
  ],
  'Additional Resources': [
    { id: 20, link: 'https://www.doi.gov/tribes/tribal-consultation-policy', description: 'U.S. Department of the Interior Tribal Consultation Policy' },
    { id: 21, link: 'https://www.aspeninstitute.org/programs/native-american-youth-leadership-program/center-for-native-american-youth/', description: 'Center for Native American Youth leadership programs' },
    { id: 22, link: 'https://pewenvironment.org', description: 'Pew Charitable Trusts—policy and environmental work including Native issues' },
    { id: 23, link: 'https://nativephilanthropy.org/', description: 'Native Americans in Philanthropy—funding and partnerships for Native communities' }
  ]
};

const filteredScholarships = computed(() => {
  const query = props.searchQuery.toLowerCase();
  if (!query) return scholarships;

  const filtered: Record<string, Scholarship[]> = {};
  
  Object.entries(scholarships).forEach(([category, items]) => {
    const matchingItems = items.filter(scholarship => 
      scholarship.description.toLowerCase().includes(query) ||
      scholarship.link.toLowerCase().includes(query)
    );
    
    if (matchingItems.length > 0) {
      filtered[category] = matchingItems;
    }
  });
  
  return filtered;
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
    <div class="space-y-8">
      <template v-for="(items, category) in filteredScholarships" :key="category">
        <section v-if="items.length > 0">
          <h2 class="text-xl font-semibold mb-4 text-[#37352f] dark:text-[#fbfbfa]">{{ category }}</h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-3">
            <div
              v-for="scholarship in items"
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