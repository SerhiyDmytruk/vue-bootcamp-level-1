<script setup>
import { ref } from "vue";
import UserProfileCard from "@/components/UserProfileCard.vue";
import UserProfileCardEdit from "@/components/UserProfileCardEdit.vue";

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

function updated(data) {
  console.log(data);
  user.value = data;
}

function localAlert() {
  alert(`You messaged ${user.value.name.first} ${user.value.name.last}`);
}
</script>
<template>
  <div class="viewport-center">
    <UserProfileCard v-bind="user" @message="localAlert" class="mr-5" />
    <UserProfileCardEdit v-bind="user" @saved="updated" />
  </div>
</template>

<style scoped></style>
