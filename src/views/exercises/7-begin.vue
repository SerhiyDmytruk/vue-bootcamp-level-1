<script setup>
import { ref } from "vue";
import UserProfileCard from "@/components/UserProfileCard.vue";

const user = ref({
  avatar: "https://i.pravatar.cc/150?img=6",
  username: "dudemcface",
  bio: "I'm a software engineer focused on delivering unique and engaging experiences.",
  name: {
    first: "Dude",
    last: "McFace",
  },
  skills: ["JavaScript", "Vue", "React", "Node"],
  pro: true,
});
</script>
<template>
  <div class="exercise-7">
    <UserProfileCard
      :avatar="user.avatar"
      :username="user.username"
      :pro="user.pro"
      :name="user.name"
      :skills="user.skills.sort()"
      :bio="user.bio"
    />
  </div>
</template>

<style scoped>
.exercise-7 {
  @apply flex items-center justify-center h-screen p-10;
}
</style>
