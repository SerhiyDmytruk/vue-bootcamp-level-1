<script setup lang="ts">
import VueMarkdown from "vue-markdown-render";
import {
  ref,
  computed,
  onMounted,
  onUnmounted,
  watch,
  useTemplateRef,
} from "vue";
import { refAutoReset } from "@vueuse/core";
import AppModal from "@/components/internal/AppModal.vue";
import { onBeforeRouteLeave } from "vue-router";

const props = defineProps<{
  repo: string;
  instructions: string;
  openFiles: string;
  checklist: string[];
  completeBtnText?: string;
}>();

defineEmits(["next"]);

const instructionsOpen = ref(false);
function closeInstructions() {
  instructionsOpen.value = false;
}

const showChecklist = ref(false);
const checked = ref([]);

const iframe = useTemplateRef("iframe");

//iframe concerns
const showResetConfirm = ref(false);
const iframeUrl = computed(() => {
  if (refreshingFrame.value) return "";
  return `https://stackblitz.com/github/${props.repo}?embed=1&file=${props.openFiles}`;
});

const refreshingFrame = refAutoReset(false, 20);

function refreshIframe() {
  refreshingFrame.value = true;
}

function addReloadConfirm() {
  window.onbeforeunload = function () {
    return "Are you sure you want to leave?";
  };
}

function removeReloadConfirm() {
  window.onbeforeunload = () => {};
}

onMounted(addReloadConfirm);
onUnmounted(removeReloadConfirm);

watch(
  () => checked.value.length,
  () => {
    if (checked.value.length === props.checklist.length) {
      removeReloadConfirm();
    } else {
      addReloadConfirm();
    }
  },
);

onBeforeRouteLeave(() => {
  if (checked.value.length === props.checklist.length) return true;
  const answer = window.confirm("Are you sure you want to leave?");
  if (!answer) return false;
});
</script>
<template>
  <!-- Main Content -->
  <div class="flex flex-col justify-between h-screen">
    <div
      class="flex justify-between p-4 mt-[60px] bg-gray-300 dark:bg-gray-600"
    >
      <button class="btn btn-outline" @click="instructionsOpen = true">
        Instructions
      </button>
      <a href="https://vuejs.org" target="_blank" class="btn btn-outline"
        >Vue.js Docs</a
      >
    </div>
    <div class="h-full">
      <iframe
        ref="iframe"
        :src="iframeUrl"
        frameborder="0"
        class="w-full h-full"
      ></iframe>
    </div>

    <!-- Reset Confirm Modal -->
    <AppModal :open="showResetConfirm" @close="showResetConfirm = false">
      <h2 class="mb-5 text-2xl font-bold">
        Are you sure you want to reset the code?
      </h2>
      <p>All changes will be lost.</p>
      <div class="flex justify-between mt-5">
        <button @click="showResetConfirm = false" class="btn btn-outline">
          Cancel
        </button>
        <button
          @click="
            refreshIframe();
            showResetConfirm = false;
          "
          class="btn btn-primary"
        >
          Reset
        </button>
      </div>
    </AppModal>
    <div class="z-50 flex justify-between p-4 bg-gray-300 dark:bg-gray-600">
      <button class="btn btn-outline" @click="showResetConfirm = true">
        <Icon icon="fa:refresh"></Icon>
        Reset
      </button>
      <button class="btn btn-outline" @click="showChecklist = true">
        Next Challenge
        <Icon icon="fa:chevron-right"></Icon>
      </button>
    </div>
  </div>

  <!-- Instructions Drawer -->
  <Transition :name="'slide'">
    <div
      v-if="instructionsOpen"
      v-click-outside="closeInstructions"
      class="fixed top-0 bottom-0 left-0 z-[999999999] bg-base-300 max-w-xl h-full"
    >
      <div class="h-full p-5 overflow-y-scroll prose">
        <vue-markdown :source="instructions" />
      </div>
    </div>
  </Transition>

  <!-- Checklist -->
  <AppModal :open="showChecklist" @close="showChecklist = false">
    <h2
      class="flex items-center gap-4 pb-2 mb-2 text-2xl border-b border-gray-300 dark:border-gray-800"
    >
      <Icon icon="fa:check" class="text-primary" />
      Exercise Checklist
    </h2>
    <h3 class="mb-4 text-lg">
      Double check that you've met all the requirements.
    </h3>
    <p class="text-sm">
      Ensure that your solution addresses all of the following items.
    </p>
    <p class="text-sm">
      Missing even one of these items will result in a failed exam.
    </p>
    <ul class="flex flex-col gap-4 p-4 mt-5 rounded-lg bg-base-300">
      <li v-for="item in checklist" :key="item" class="flex gap-4 items-top">
        <input
          type="checkbox"
          class="checkbox"
          v-model="checked"
          :value="item"
        />
        <span>{{ item }}</span>
      </li>
    </ul>

    <slot name="after-checklist"></slot>

    <div class="flex justify-between mt-5">
      <button @click="showChecklist = false" class="btn btn-outline">
        Continue Coding
      </button>
      <button
        :disabled="checked.length !== checklist.length"
        @click="$emit('next')"
        class="btn btn-primary"
      >
        {{ completeBtnText || "Complete" }}
      </button>
    </div>
  </AppModal>
</template>
<style scoped>
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.5s ease;
}

.slide-enter-from,
.slide-leave-to {
  transform: translateX(-100%);
}
</style>
