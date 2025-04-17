<script setup>
import Header from './components/Header.vue';
import Uses from './components/Uses.vue';
import Projects from './components/Projects.vue';
import Footer from './components/Footer.vue';
import RecentTracks from './components/RecentTracks.vue';
import BlogList from './components/wordpress.vue'; 
</script>

<template>
  <div class="sm:pt-20 max-w-screen-lg mx-auto p-5 relative">
    <div class="z-0 absolute -mt-10 right-0 text-[10rem] opacity-10 select-none">🌊</div>
    <div class="relative mb-10"><header /></div>
    <div class="mb-10"><uses /></div>
    <div class="z-0 absolute mt-10 text-[10rem] opacity-10 select-none">🐧</div>
    <div class="relative mb-10"><projects /></div>
    <div class="relative mb-10"><RecentTracks /></div>
    <div class="relative mb-10"><Blog /></div>

    <Footer />
  </div>
</template>
