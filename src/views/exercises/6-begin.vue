<script setup>
import { ref, reactive, computed } from "vue";

const form = reactive({
  email: "",
  password: "",
  confirmPassword: "",
  interests: [],
  terms: "",
});

const regxEm =
  /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|.(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;

const emailValid = ref(true);
const passwordValid = ref(true);
const confirmPasswordValid = ref(true);
const interestsValid = ref(true);
const termsValid = ref(true);

function onSubmit() {
  handlerValidateEmail();
  handlerValidatePassword();
  handlerValidateConfirmPassword();
  handlerValidateInterest();
  handlerValidateTerms();

  if (
    emailValid.value !== false &&
    passwordValid.value !== false &&
    confirmPasswordValid.value !== false &&
    interestsValid.value.length !== false &&
    termsValid.value !== false
  ) {
    console.log(form);
  } else {
    alert("False");
  }
  return;
}

function handlerValidateEmail() {
  if (form.email === null) {
    emailValid.value = false;
  }

  emailValid.value = regxEm.test(form.email);
}

function handlerValidatePassword() {
  if (form.password !== "" && form.password.length !== 0)
    passwordValid.value = true;
}

function handlerValidateConfirmPassword() {
  if (form.confirmPassword !== form.password)
    confirmPasswordValid.value = false;
}

function handlerValidateInterest() {
  if (form.interests.length) interestsValid.value = true;
}

function handlerValidateTerms() {
  if (form.termsValid === false) termsValid.value = true;
}
</script>

<template>
  <div class="page-wrapper">
    <h1>Register</h1>
    <form @submit.prevent="onSubmit">
      <label class="form-control">
        <div class="label">Email</div>
        <input
          id="email"
          type="text"
          name="email"
          class="input input-bordered"
          v-model="form.email"
          :class="{ 'input-error': !emailValid }"
          @change="handlerValidateEmail"
        />
        <div class="label" v-if="!emailValid">
          <span class="text-red-500 label-text-alt"
            >A valid email is required</span
          >
        </div>
      </label>

      <label class="form-control">
        <div class="label">Password</div>
        <input
          id="password"
          type="password"
          name="password"
          class="input input-bordered"
          v-model="form.password"
          :class="{ 'input-error': !passwordValid }"
          @change="handlerValidatePassword"
        />
        <!-- Show if invalid -->
        <div class="label" v-if="!passwordValid">
          <span class="text-red-500 label-text-alt">Password is required</span>
        </div>
      </label>

      <label class="form-control">
        <div class="label">Confirm Password</div>
        <input
          id="confirm_password"
          type="password"
          name="confirm_password"
          class="input input-bordered"
          v-model="form.confirmPassword"
          :class="{ 'input-error': !confirmPasswordValid }"
          @change="handlerValidateConfirmPassword"
        />
        <!-- Show if invalid -->
        <div class="label" v-if="!confirmPasswordValid">
          <span class="text-red-500 label-text-alt">Passwords must match</span>
        </div>
      </label>

      <div class="form-control">
        <label class="label">Interested in... {{ form.interests }}</label>

        <div class="flex gap-5">
          <label class="flex items-center">
            <input
              type="checkbox"
              name="interests"
              value="hiking"
              v-model="form.interests"
              @change="handlerValidateInterest"
            />Hiking
          </label>
          <label class="flex items-center">
            <input
              type="checkbox"
              name="interests"
              value="biking"
              v-model="form.interests"
              @change="handlerValidateInterest"
            />
            Biking
          </label>
          <label class="flex items-center">
            <input
              type="checkbox"
              name="interests"
              value="camping"
              v-model="form.interests"
              @change="handlerValidateInterest"
            />
            Camping
          </label>
          <label class="flex items-center">
            <input
              type="checkbox"
              name="interests"
              value="skiing"
              v-model="form.interests"
              @change="handlerValidateInterest"
            />
            Skiing
          </label>
        </div>

        <div class="label" v-if="!interestsValid">
          <span class="mt-5 text-red-500 label-text-alt"
            >Must select at least one interest</span
          >
        </div>
      </div>

      <hr class="my-5 opacity-20" />

      <label class="flex items-center">
        <input
          type="checkbox"
          name="terms"
          class="w-auto checkbox checkbox-primary"
          v-model="form.terms"
          @change="handlerValidateTerms"
        />
        <span class="label-text">I agree to the terms and conditions</span>
      </label>

      <div class="label" v-if="!termsValid">
        <span class="mt-5 text-red-500 label-text-alt">
          You must agree to the terms and conditions
        </span>
      </div>

      <button class="block w-full mt-5 btn btn-primary" type="submit">
        Register
      </button>
    </form>
  </div>
</template>

<style scoped>
.page-wrapper {
  @apply w-[960px] m-auto mt-20;
}
h1 {
  @apply text-3xl mb-5;
}

h2 {
  @apply text-xl mb-3;
}

input,
textarea,
select {
  @apply block mb-3 w-full;
}
input[type="checkbox"] {
  @apply checkbox inline-block my-0 mr-3;
}
</style>
