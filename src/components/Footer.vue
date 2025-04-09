<script setup>
import { ref, onMounted } from 'vue';

const commit = ref(null);

onMounted(async () => {
})
</script>

<template>
  <div class="flex justify-between mt-10 gap-5 text-sm text-catppuccin-gray">
    <a href="https://github.com/d-niz/dniz.tr" target="_blank" class="whitespace-nowrap underline">
      site repo
    </a>
    
    <span class="whitespace-nowrap">
      GPG: 0x433B49A88191C661
    </span>
  </div>
</template>
