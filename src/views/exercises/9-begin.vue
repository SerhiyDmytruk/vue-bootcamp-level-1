<script setup>
import { ref } from "vue";
import UserProfileCardAdvanced from "@/components/UserProfileCardAdvanced.vue";
import UserProfileCardEdit from "@/components/end/UserProfileCardEdit.vue";
import CardFlip from "@/components/CardFlip.vue";

const user = ref({
  avatar: "https://i.pravatar.cc/150?img=6",
  username: "dudemcface",
  bio: "I'm a software engineer focused on delivering <strong>unique and engaging experiences</strong>.",
  name: {
    first: "Dude",
    last: "McFace",
  },
  skills: ["JavaScript", "Vue", "React", "Node"],
  pro: true,
});

const editing = ref(false);
</script>
<template>
  <div class="exercise-11">
    <div>
      <UserProfileCardEdit
        v-if="editing"
        v-bind="user"
        @saved="user = $event"
      />
      <UserProfileCardAdvanced v-else v-bind="user" />

      <button @click="editing = !editing" class="edit-button">
        {{ editing ? "Cancel" : "Edit" }}
      </button>
    </div>
  </div>
</template>

<style scoped>
.exercise-11 {
  @apply flex items-center justify-center h-screen p-10;
}
.edit-button {
  @apply bg-gray-200 dark:bg-gray-700 dark:text-white px-2 py-1 rounded block mt-2 w-full max-w-xs;
}
</style>
