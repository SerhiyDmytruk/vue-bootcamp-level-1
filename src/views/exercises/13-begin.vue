<script setup>
import { onMounted, ref, computed, onUnmounted, useTemplateRef } from "vue";
const el = useTemplateRef("el");

const numOfSlides = 10;
const activeSlide = ref(1);
let elWidth = ref(600);
const speed = 3000;

onMounted(() => {
  setElWidth();
  window.addEventListener("resize", setElWidth);
});

function setElWidth() {
  if (!el.value) return;
  elWidth.value = el.value.clientWidth;
}

function rotateSlide() {
  if (activeSlide.value === numOfSlides) {
    activeSlide.value = 1;
  } else {
    activeSlide.value++;
  }
}

const translateX = computed(() => {
  if (!el.value) return;
  return `translateX(-${elWidth.value * (activeSlide.value - 1)}px)`;
});

let interval;
const play = () => (interval = setInterval(rotateSlide, speed));
const stop = () => clearInterval(interval);

play();

onUnmounted(() => {
  window.removeEventListener("resize", setElWidth);
  stop();
});
</script>
<template>
  <div class="flex items-center justify-center h-screen px-5">
    <div
      class="slides-wrapper"
      ref="el"
      :style="{ width: elWidth ? `${elWidth}px` : 'auto' }"
    >
      <img
        v-for="n in numOfSlides"
        :key="n"
        :src="`https://i.pravatar.cc/1200?img=${n}`"
        class="slide"
        :style="{ transform: translateX }"
        @mouseenter="stop()"
        @mouseleave="play()"
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

.v-enter-active,
.v-leave-active {
  @apply transition-transform duration-700 absolute top-0 left-0 right-0 bottom-0;
}
.v-enter-from {
  transform: translateX(100%);
}
.v-leave-to {
  transform: translateX(-100%);
}
</style>
