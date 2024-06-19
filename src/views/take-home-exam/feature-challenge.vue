<script setup lang="ts">
import AppModal from "@/components/internal/AppModal.vue";
import CodingChallenge from "@/components/internal/CodingChallenge.vue";
import { useTakeHomeQuiz } from "@/composables/useTakeHomeQuiz";
import { useRouter } from "vue-router";
import VueMarkdown from "vue-markdown-render";
import { ref } from "vue";
const router = useRouter();
const { status } = useTakeHomeQuiz();

if (status.value !== "DoingFeatureChallenge") {
  router.replace("/take-home-exam");
}

const openFiles = "src/components/FileTree.vue";

const instructions = `# Rating Input Feature Challenge

In this challenge you are tasked with creating a star rating input component.

The challenge will require that you work in \`RatingInput.vue\` and refrence \`App.vue\`.

## Requirements

### the Rating Input should:

1. Accept the following props:

   - \`modelValue\` - this represents the rating the user has picked and allows the component to work with v-model
   - \`count\` - this is the total number of stars the rating should display (default is 5)

2. Emit the following events:

   - update:modelValue - emit when a star is clicked on. The payload is the rating (1, 2, 3, etc)
   - You should also validate the event payload to ensure that it is a number

3. Display a button for each star based on the provided count

4. Display solid yellow stars for stars less than or equal to the \`modelValue\`

5. Display outlined gray stars for stars greater than the \`modelValue\`

6. Support the following keyboard shortcuts when the rating wrapper is focused

   - Pressing keyboard numbers 0-9 should set the rating accordingly
   - Pressing the \`ArrowUp\` or \`ArrowRight\` key should increment the rating by 1
   - Pressing the \`ArrowDown\` or \`ArrowLeft\` key should decrement the rating by 1

7. Apply the class \`perfect-rating\` to the \`rating-wrapper\` div when a perfect rating is selected

> 💡 HINT: The stars will turn orange when the perfect-rating class is applied to the \`rating-wrapper\`


By the end of the exercise you should have something that looks like this:
![screenshot of completed](/bootcamp-level-1-take-home-feature-challenge.gif)


## Other Considerations

- If you see the \`data-test\` attribute in the boilerplate don't remove it. If you remove it, your code may be invalid for the certificate.
`;

const checklist = [
  "The component displays the proper number of stars based on the count prop",
  "The user can control the rating with the arrow and number keys",
  "The user can click on a star to set the rating",
  "The component accepts the `modelValue` and `count` props",
  "The component emits an `update:modelValue` event when a star is clicked",
  "The component displays outlined or solid stars properly based on the selected rating (`modelValue`)",
  "The component wrapper div includes the class `perfect-rating` and the stars display orange when a perfect rating is given",
];

function handleNext() {
  status.value = "DoingBugChallenge";
  router.push("/take-home-exam/bug-challenge");
}

const modalOpen = ref(true);
</script>
<template>
  <CodingChallenge
    repo="Vuejs-Certification/bootcamp-level-1-take-home-feature-challenge/tree/start"
    :instructions="instructions"
    :openFiles="openFiles"
    :checklist="checklist"
    @next="handleNext"
  >
  </CodingChallenge>
  <AppModal :open="modalOpen" @close="modalOpen = false">
    <h1 class="mb-3 text-lg font-bold text-primary">
      Coding Challenge #1 - Feature Challenge
    </h1>

    <div class="p-3 overflow-scroll prose-sm prose rounded h-96">
      <vue-markdown :source="instructions" />
    </div>
    <div class="flex justify-end mt-5">
      <button class="btn btn-primary" @click="modalOpen = false">
        Begin Feature Challenge
      </button>
    </div>
  </AppModal>
</template>
