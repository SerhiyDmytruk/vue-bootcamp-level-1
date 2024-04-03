<script setup>
import { ref } from "vue";
const name = ref("Bootcampers");
const item = ref("coffee");
</script>
<template>
  <div class="viewport-center">
    <div>
      <label for="name" class="block"
        >Name:
        <input id="name" name="name" type="text" v-model="name" class="input" />
      </label>
      <label for="coffe" class="block"
        >Item:
        <input
          id="coffe"
          name="coffe"
          type="text"
          v-model="item"
          class="input"
        />
      </label>
      <hr />
      <div class="mt-5">Hello {{ name }}! Do you like {{ item }}?</div>
    </div>
  </div>
</template>
