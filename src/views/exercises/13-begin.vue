<script setup>
import { useTemplateRef } from "vue";
const el = useTemplateRef("el");

const numOfSlides = 10;
</script>
<template>
  <div class="flex items-center justify-center h-screen px-5">
    <div class="slides-wrapper" ref="el">
      <img
        v-for="n in numOfSlides"
        :key="n"
        :src="`https://i.pravatar.cc/1200?img=${n}`"
        class="slide"
      />
    </div>
  </div>
</template>

<style scoped>
.slide {
  @apply block object-cover w-full transition-all duration-700;
}
.slides-wrapper {
  @apply flex overflow-hidden rounded max-w-[600px];
}
</style>
