<script setup lang="ts">
import { onMounted, useTemplateRef } from "vue";
const el = useTemplateRef("el");

onMounted(() => {
  if (!el.value) return;
  console.log(el.value.innerText);
});
</script>
<template>
  <div>
    <p ref="el">Hello World</p>
  </div>
</template>
