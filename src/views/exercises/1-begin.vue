<script setup>
import { ref } from "vue";
const name = ref("Bootcampers");
const item = ref("coffee");
</script>
<template>
  <div class="viewport-center">
    <div>
      <div class="block">
        <label for="name">Name:<input type="text" v-model="name" id="name" class="input">
        </label>
      </div>
      <div class="block">
        <label for="item">Item:<input type="text" v-model="item" id="item" class="input">
        </label>
      </div>
      <hr>
      <div class="mt-5">Hello {{name}}! Do you like {{item}}?</div>
    </div>
  </div>
</template>
