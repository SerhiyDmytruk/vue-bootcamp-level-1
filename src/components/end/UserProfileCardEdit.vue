<script setup>
import { ref } from "vue";

const props = defineProps({
  avatar: {
    type: String,
    default: "/placeholder-avatar.jpg",
  },
  username: {
    type: String,
    required: true,
  },
  bio: {
    type: String,
    default: "",
  },
  name: {
    type: Object,
    default: () => ({ first: "John", last: "Doe" }),
  },
  skills: {
    type: Array,
    default: () => [],
    validator: (skills) => {
      return skills.length > 0;
    },
  },
  pro: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits({
  saved: (user) => {
    return user.username && user.skills.length > 0;
  },
});

const _user = ref({
  ...JSON.parse(JSON.stringify(props)),
  skills: props.skills.join(", "),
});

function submitForm() {
  emit("saved", {
    ...JSON.parse(JSON.stringify(_user.value)),
    skills: _user.value.skills
      .split(",")
      .map((skill) => skill.trim())
      .filter((skill) => skill),
  });
}
</script>

<template>
  <form @submit.prevent="submitForm" class="profile-form">
    <!-- Username Field -->
    <div class="form-field">
      <label for="username" class="form-label">Username</label>
      <input
        v-model="_user.username"
        type="text"
        id="username"
        class="form-input"
      />
    </div>

    <!-- Bio Field -->
    <div class="form-field">
      <label for="bio" class="form-label">Bio</label>
      <textarea
        v-model="_user.bio"
        id="bio"
        class="form-textarea"
        rows="3"
      ></textarea>
    </div>

    <!-- Name Fields -->
    <div class="name-fields">
      <div class="form-field">
        <label for="firstName" class="form-label">First Name</label>
        <input
          v-model="_user.name.first"
          type="text"
          id="firstName"
          class="form-input"
        />
      </div>
      <div class="form-field">
        <label for="lastName" class="form-label">Last Name</label>
        <input
          v-model="_user.name.last"
          type="text"
          id="lastName"
          class="form-input"
        />
      </div>
    </div>

    <!-- Skills Field -->
    <div class="form-field">
      <label for="skills" class="form-label">Skills (comma-separated)</label>
      <input
        v-model="_user.skills"
        type="text"
        id="skills"
        class="form-input"
        placeholder="e.g., HTML,CSS,JavaScript"
      />
    </div>

    <!-- Pro Checkbox -->
    <div class="flex justify-between">
      <label class="form-check-label">
        <input type="checkbox" v-model="_user.pro" class="form-checkbox" />
        Pro User
      </label>

      <button class="submit-button" type="submit">Save Profile</button>
    </div>
  </form>
</template>
<style scoped>
.profile-form {
  @apply bg-white dark:bg-base-200 rounded-lg overflow-hidden shadow-md max-w-xs h-[420px] px-5 py-3 border-t-8 border-t-blue-500;
}

.form-field,
.name-fields,
.form-field-check,
.form-submit {
  @apply mb-3 text-sm;
}

.name-fields {
  @apply grid grid-cols-2 gap-4 mb-0;
}

.form-label {
  @apply block text-gray-700 text-sm font-bold mb-2 dark:text-gray-200;
}

.form-input,
.form-textarea {
  @apply shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none dark:bg-gray-900 dark:text-gray-200;
}

.form-textarea {
  @apply resize-none;
}

.form-check-label {
  @apply block text-gray-700 text-sm font-bold mb-2 dark:text-gray-200;
}

.form-checkbox {
  @apply mr-2 leading-tight;
}

.submit-button {
  @apply bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none;
}
</style>
