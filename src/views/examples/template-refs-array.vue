<script setup>
import { onMounted, useTemplateRef } from "vue";
const allElements = useTemplateRef("elements");

onMounted(() => {
  allElements.value.forEach((el) => {
    console.log(el);
  });
});
</script>

<template>
  <div v-for="n in 10" :key="n" ref="elements">
    {{ n }}
  </div>
</template>
