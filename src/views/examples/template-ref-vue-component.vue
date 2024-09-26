<script setup lang="ts">
import { useTemplateRef } from "vue";
import ComponentWithExposedMethod from "@/components/examples/ComponentWithExposedMethod.vue";
const component = useTemplateRef("component");
</script>
<template>
  <ComponentWithExposedMethod ref="component" />
  <button @click="component?.toggleActive()">Toggle Active</button>
</template>
