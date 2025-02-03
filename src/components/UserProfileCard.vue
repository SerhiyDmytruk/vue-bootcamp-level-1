<script setup>
// you must define the props here to make the component display the proper content
const props = defineProps({
  pro: {
    type: Boolean,
    default: false,
    required: true,
  },
  name: {
    first: {
      type: String,
      default: "John",
    },
    last: {
      type: String,
      default: "Doe",
    },
  },
  bio: {
    type: String,
    required: true,
  },
  username: {
    type: String,
    required: true,
  },
  avatar: {
    type: String,
    default: "placeholder-avatar.jpg",
  },
  skills: {
    type: Array,
    required: true,
  },
});
</script>
<template>
  <div class="profile-card">
    <div class="header">
      <span v-if="pro" class="badge">PRO</span>
      <div class="avatar-wrapper">
        <img :src="avatar" :alt="`${name.first} ${name.last}`" class="avatar" />
      </div>
    </div>
    <div class="body">
      <h3 class="name">{{ name.first }} {{ name.last }}</h3>
      <h4 class="username">@{{ username }}</h4>
      <p class="bio">{{ bio }}</p>
      <div class="actions">
        <button class="message-btn">Message</button>
        <button class="follow-btn">Follow</button>
      </div>
    </div>
    <div class="footer">
      <ul class="skills">
        <li v-for="skill in skills" :key="skill">{{ skill }}</li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
/* Card Design based on this article from Florin Pop
www.florin-pop.com/blog/2019/04/profile-card-design/
*/
.profile-card {
  @apply bg-white dark:bg-base-200 rounded-lg overflow-hidden shadow-md max-w-xs;
}

.header {
  @apply bg-blue-500 p-4 text-white relative;
}

.badge {
  @apply absolute top-2 right-2 bg-yellow-300 text-yellow-800 py-1 px-2 rounded text-xs;
}

.avatar-wrapper {
  @apply flex justify-center;
}

.avatar {
  @apply rounded-full border-4 border-white h-24 w-24 object-cover;
}

.body {
  @apply p-4 text-center;
}

.name {
  @apply text-lg font-bold;
}

.username {
  @apply text-gray-500 text-sm;
}

.bio {
  @apply text-gray-700 my-2 dark:text-gray-500;
}

.actions {
  @apply flex justify-center mt-4 gap-3;
}

.message-btn,
.follow-btn {
  @apply bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-300;
}

.footer {
  @apply bg-gray-100 p-4 flex justify-center dark:bg-base-300;
}

.skills {
  @apply list-none flex flex-wrap justify-center gap-2 items-center;
}

.skills li {
  @apply bg-gray-200 text-gray-800 py-1 px-2 rounded text-xs dark:bg-base-100 dark:text-gray-300;
}
</style>
