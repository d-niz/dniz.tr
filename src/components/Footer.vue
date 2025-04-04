<script setup>
import { ref, onMounted } from 'vue';

const commit = ref(null);

onMounted(async () => {
  // commit verisi artık alınmayacak
})
</script>

<template>
  <div class="flex justify-between mt-10 gap-5 text-sm text-catppuccin-gray">
    <!-- GitHub bağlantısını gösteriyoruz -->
    <a href="https://github.com/d-niz/dniz.tr" target="_blank" class="whitespace-nowrap underline">site repo</a>
  </div>
</template>
