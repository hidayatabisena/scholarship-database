<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import { XMarkIcon } from '@heroicons/vue/24/outline';

const props = defineProps<{
  isOpen: boolean;
  tallyFormUrl: string;
}>();

const emit = defineEmits(['close']);

const iframeLoaded = ref(false);

const closeModal = () => {
  emit('close');
};

// Escape key to close modal
const handleKeyDown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    closeModal();
  }
};

onMounted(() => {
  document.addEventListener('keydown', handleKeyDown);
});

watch(() => props.isOpen, (newValue) => {
  if (newValue) {
    // Prevent body scrolling when modal is open
    document.body.style.overflow = 'hidden';
  } else {
    // Restore body scrolling when modal is closed
    document.body.style.overflow = '';
  }
});
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-50 overflow-y-auto">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">

      <div class="fixed inset-0 transition-opacity" @click="closeModal">
        <div class="absolute inset-0 bg-gray-500 dark:bg-gray-900 opacity-75"></div>
      </div>

      <!-- Modal panel -->
      <div 
        class="inline-block align-bottom bg-white dark:bg-gray-800 rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full relative"
        role="dialog" 
        aria-modal="true" 
        aria-labelledby="modal-headline"
      >
        <div class="absolute top-2 right-2 z-10">
          <button
            type="button"
            class="bg-white dark:bg-gray-800 rounded-md text-gray-400 hover:text-gray-500 dark:hover:text-gray-300 focus:outline-none focus:ring-2 focus:ring-blue-500 p-1"
            @click="closeModal"
          >
            <span class="sr-only">Close</span>
            <XMarkIcon class="h-4 w-4" />
          </button>
        </div>
        
        <div class="p-4 sm:p-6">
          <div class="w-full h-[600px] relative">
            <div v-if="!iframeLoaded" class="absolute inset-0 flex items-center justify-center">
              <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-gray-900 dark:border-gray-100"></div>
            </div>
            <iframe
              :src="tallyFormUrl"
              frameborder="0"
              marginheight="0"
              marginwidth="0"
              class="w-full h-full"
              @load="iframeLoaded = true"
            ></iframe>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>