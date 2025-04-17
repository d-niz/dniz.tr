<script setup>
import { ref, onMounted } from 'vue';

const posts = ref([]);

onMounted(async () => {
  try {
    const response = await fetch('https://denzz.xyz/blog/wp-json/wp/v2/posts?per_page=2&categories=7');
    const data = await response.json();
    posts.value = data;
  } catch (error) {
    console.error('Blog yazıları alınamadı.');
  }
});
</script>

<template>
  <div class="mb-2 font-black text-2xl">blog/</div>
  <div class="grid md:grid-cols-2 gap-2">
    <div v-if="!posts.length">blog posts could not be retrieved.</div>
    <a
      v-for="post in posts"
      :key="post.id"
      :href="post.link"
      target="_blank"
      class="project-card flex flex-col justify-between px-5 py-3 bg-[#181825]/[.3] border-[#585b70] border-[0.5px] rounded-lg text-sm"
    >
      <div class="font-bold text-lg" v-html="post.title.rendered" />
      <div class="text-sm text-catppuccin-gray" v-html="post.excerpt.rendered" />
      <div class="mt-2 text-xs text-[#a6adc8]">
        Yayınlanma: {{ new Date(post.date).toLocaleDateString('tr-TR') }}
      </div>
    </a>
  </div>
</template>

<style scoped>
.project-card {
  transition: all 0.3s ease;
}
.project-card:hover {
  background-color: #313244;
  border-color: #cdd6f4;
}
</style>
