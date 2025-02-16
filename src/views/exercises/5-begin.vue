<script setup>
import { ref, computed } from "vue";
// prettier-ignore
const users = ref([
  { id: 'f755ce63-8284-4304-bb7e-2449a57c39b9', votes: 0, name: 'Debra Hunt', avatar: 'https://i.pravatar.cc/150?img=1' },
  { id: 'debcef41-0a1e-4d57-a56f-f7064fa80e60', votes: 0, name: 'Michael Perkins', avatar: 'https://i.pravatar.cc/150?img=2' },
  { id: '4f51bc5e-0f57-4a0c-9de5-3df063316ce2', votes: 0, name: 'Jessica Martin', avatar: 'https://i.pravatar.cc/150?img=3' },
  { id: '3abaf52f-9ddf-451d-9107-e0edabbf3eff', votes: 0, name: 'Karen Clark', avatar: 'https://i.pravatar.cc/150?img=4' },
  { id: '9a079fca-16ac-401e-b26a-35b2b6158fe6', votes: 0, name: 'Nancy Green', avatar: null },
  { id: 'b08e12dd-8656-43e4-8b42-b54db36ce3fe', votes: 0, name: 'Audrey Williamson', avatar: 'https://i.pravatar.cc/150?img=6' },
  { id: '89d8ffb9-98be-48a2-9346-af167dc6685c', votes: 0, name: 'Cynthia Anderson', avatar: null },
  { id: '5222996a-e747-47cb-8e3c-f0171208e17b', votes: 0, name: 'Ryan Guzman', avatar: null },
  { id: '7fbee0ff-c831-44ed-b7bb-c8265cc4fa4f', votes: 0, name: 'Sean Bauer', avatar: 'https://i.pravatar.cc/150?img=9' },
  { id: '9c110067-2345-4f7f-89cc-b50cd02fd106', votes: 0, name: 'Ashley Fisher', avatar: 'https://i.pravatar.cc/150?img=10' }
]);

const top3Users = computed(() => {
  return [...users.value]
    .sort((a, b) => b.votes - a.votes)
    .slice(0, 3)
    .filter((user) => user.votes > 0);
});

function incrementVote(user) {
  user.votes++;
}

function decrementVote(user) {
  if (user.votes > 0) user.votes--;
}
</script>
<template>
  <div class="viewport-center">
    <div>
      <div class="leader-board">
        <h2>Leader Board</h2>
        <ul v-if="top3Users.length">
          <li v-for="user in top3Users" :key="user.id">
            <strong>{{ user.name }} </strong>
            <span class="votes-pill">{{ user.votes }}</span>
          </li>
        </ul>
        <p v-else>No one has any votes yet</p>
      </div>
      <h2>Candidates</h2>
      <ul class="user-wrapper">
        <li
          class="user-card"
          v-for="(user, index) in users"
          :key="user.id"
          :tabindex="index + 1"
          @keydown.arrow-up="incrementVote(user)"
          @keydown.arrow-down="decrementVote(user)"
          :class="{
            winning: user.votes === top3Users[0].values,
            'losing-hard': user.votes === 0
          }"
        >
          <img class="avatar" :src="user.avatar || '/placeholder-avatar.jpg'" />
          <div>
            <p class="mb-2">{{ user.name }}</p>
            <button @click="user.votes++">Vote {{ user.votes }}</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.user-wrapper {
  @apply flex flex-wrap gap-10 justify-center mt-4;
}
.user-card {
  @apply p-3 shadow-lg border border-slate-200 dark:border-slate-950 rounded w-80 relative flex items-center;
}
.avatar {
  @apply font-bold object-cover w-24 h-24 rounded-full mr-5 dark:bg-slate-700 bg-slate-200 inline-flex justify-center items-center text-3xl;
}

button {
  @apply bg-blue-600 rounded-md border border-blue-800 px-2  text-white;
}
.leader-board {
  @apply text-center mb-10;
}

h2 {
  @apply text-3xl text-center;
}

.leader-board ul {
  @apply flex justify-center gap-5 text-lg;
}
.votes-pill {
  @apply bg-gray-700 p-2 rounded-full text-white text-sm ml-2 w-8 h-8 inline-block text-center leading-4;
}
.winning {
  @apply border-green-600;
}
.losing-hard {
  @apply border-red-600;
}
</style>
