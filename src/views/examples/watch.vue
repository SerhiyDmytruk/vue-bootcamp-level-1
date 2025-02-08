<script setup lang="ts">
import { ref, watchEffect, watch } from "vue";

// const myTeamsScore = ref(7);
// const otherTeamsScore = ref(0);
// const scores = ref([7, 0]);

// watch(
//   footballScore,
//   () => {
//     console.log(footballScore.value);
//   },
//   {
//     deep: true,
//   },
// );

const footballScore = ref({
  myTeam: 7,
  otherTeam: 0,
});

watchEffect(() => {
  console.log(footballScore.value, "footballScore");
});

// watch(
//   [myTeamsScore, otherTeamsScore],
//   ([myScore, otherScore]) => {
//     if (myScore > otherScore) {
//       alert("Hoorah! 🎉");
//     } else {
//       alert("sad day 😢");
//     }
//   },
//   { deep: true },
// );

// watch(
//   footballScore,
//   () => {
//     if (footballScore.value.myTeam > footballScore.value.otherTeam) {
//       alert("Hoorah! 🎉");
//     } else {
//       alert("sad day 😢");
//     }
//   },
//   { deep: true },
// );
</script>
<template>
  {{ footballScore }}
</template>
