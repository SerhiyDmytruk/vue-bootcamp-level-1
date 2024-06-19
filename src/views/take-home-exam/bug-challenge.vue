<script setup lang="ts">
import CodingChallenge from "@/components/internal/CodingChallenge.vue";
import { useTakeHomeQuiz } from "@/composables/useTakeHomeQuiz";
import AppModal from "@/components/internal/AppModal.vue";
import VueMarkdown from "vue-markdown-render";
import { ref } from "vue";

import { useRouter } from "vue-router";
const router = useRouter();
const { status } = useTakeHomeQuiz();

if (status.value !== "DoingBugChallenge") {
  router.replace("/take-home-exam");
}

const openFiles = "src/components/PlayGame.vue";

const instructions = `
# Spelling Game Code Challenge

This application is a Spelling Game that tests your spelling skills while having fun. However, there are currently some issues.

Your task is to figure out why the issues exist and fix them!

## Requirements

- Once the game is finished, it allows you to restart. However, after restarting, the countdown timer doesn't work correctly. Fix it.

- In \`PlayGame.vue\` a keydown event listener is added to the document to handle processing the input characters. This logs "processing spelling word" to the console on each keystroke. However, it still logs even after the game is over. Clean up this event listener to prevent memory leaks!

- Do NOT rename any existing variables

> 💡 HINT: Both bugs exist in \`PlayGame.vue\`

![Screenshot of the solution](/bootcamp-level-1-take-home-bug-challenge.gif)

## Other Considerations

- If you see the \`data-test\` attribute in the boilerplate don't remove it. If you remove it, your code may be invalid for the certificate.

`;

const checklist = [
  "When restarting the game, the playtime is updated correctly",
  `After time has run out, keystrokes no longer log the message "processing spelling word" to the console because you've successfully cleaned up the event listener on the document`,
];

function handleNext() {
  status.value = "Complete";
  router.push("/take-home-exam/results");
}

const modalOpen = ref(true);
</script>
<template>
  <CodingChallenge
    repo="Vuejs-Certification/bootcamp-level-1-take-home-bug-challenge/tree/start"
    :instructions="instructions"
    :openFiles="openFiles"
    :checklist="checklist"
    @next="handleNext"
  >
  </CodingChallenge>

  <AppModal :open="modalOpen" @close="modalOpen = false">
    <h1 class="mb-3 text-lg font-bold text-primary">
      Coding Challenge #2 - Bug Challenge
    </h1>

    <div class="p-3 overflow-scroll prose-sm prose rounded h-96">
      <vue-markdown :source="instructions" />
    </div>
    <div class="flex justify-end mt-5">
      <button class="btn btn-primary" @click="modalOpen = false">
        Begin Bug Challenge
      </button>
    </div>
  </AppModal>
</template>
