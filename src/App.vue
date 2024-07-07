<template>
  <div class="w-full max-w-md bg-white p-8 rounded-lg shadow-md">
    <form @submit.prevent="submitForm">
      <h2 class="text-2xl font-bold mb-6 text-center">Simple Form</h2>

      <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="name"
          >Name</label
        >
        <input
          v-model="name"
          :class="{ invalid: errors.name }"
          class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight"
          id="name"
          type="text"
          placeholder="Enter your name"
        />
        <p v-if="errors.name" class="text-red-500 text-xs mt-1">
          {{ errors.name }}
        </p>
      </div>

      <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="age"
          >Age</label
        >
        <input
          v-model="age"
          :class="{ invalid: errors.age }"
          class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight"
          id="age"
          type="number"
          placeholder="Enter your age"
          min="18"
          max="100"
        />
        <p v-if="errors.age" class="text-red-500 text-xs mt-1">
          {{ errors.age }}
        </p>
      </div>

      <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="email"
          >Email</label
        >
        <input
          v-model="email"
          :class="{ invalid: errors.email }"
          class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight"
          id="email"
          type="email"
          placeholder="Enter your email"
        />
        <p v-if="errors.email" class="text-red-500 text-xs mt-1">
          {{ errors.email }}
        </p>
      </div>

      <div class="mb-6">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="message"
          >Message</label
        >
        <textarea
          v-model="message"
          :class="{ invalid: errors.message }"
          class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight"
          id="message"
          rows="4"
          placeholder="Enter your message"
        ></textarea>
        <p v-if="errors.message" class="text-red-500 text-xs mt-1">
          {{ errors.message }}
        </p>
      </div>

      <div class="flex items-center justify-between">
        <button
          :disabled="loading"
          class="bg-blue-500 border-2 border-blue-500 hover:bg-transparent hover:text-blue-500 mx-auto text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition-all duration-300"
          type="submit"
        >
          Submit
        </button>
      </div>

      <p v-if="successMessage" class="text-green-500 text-xs mt-4 text-center">
        {{ successMessage }}
      </p>
      <p v-if="errorMessage" class="text-red-500 text-xs mt-4 text-center">
        {{ errorMessage }}
      </p>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: null,
      age: null,
      email: null,
      message: null,
      errors: {},
      loading: false,
      successMessage: null,
      errorMessage: null,
    };
  },
  methods: {
    validateForm() {
      this.errors = {};

      if (!this.name) {
        this.errors.name = "Name is required.";
      }

      if (!this.age || this.age < 18 || this.age > 100) {
        this.errors.age = "Age must be between 18 and 100.";
      }

      if (!this.email) {
        this.errors.email = "Email is required.";
      } else if (!/\S+@\S+\.\S+/.test(this.email)) {
        this.errors.email = "Email is invalid.";
      }

      if (!this.message) {
        this.errors.message = "Message is required.";
      }

      return Object.keys(this.errors).length === 0;
    },
    async submitForm() {
      if (!this.validateForm()) {
        return;
      }

      this.loading = true;
      this.successMessage = null;
      this.errorMessage = null;

      try {
        let t = new Date();
        await fetch(
          "https://abdellatif-dummy-form-default-rtdb.firebaseio.com/appliers.json",
          {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
              name: this.name,
              age: this.age,
              email: this.email,
              message: this.message,
              time: t,
            }),
          }
        );

        this.name = null;
        this.age = null;
        this.email = null;
        this.message = null;
        this.successMessage = "Form submitted successfully!";
      } catch (error) {
        this.errorMessage =
          "There was an error submitting the form. Please try again.";
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>

<style>
#app {
  @apply flex justify-center items-center h-[100vh];
}
label {
  @apply cursor-pointer relative;
}
label::before {
  content: "*";
  @apply text-red-600 absolute -left-2 -top-1;
}
input,
textarea {
  @apply focus:outline focus:shadow-lg relative;
}
.invalid {
  outline: rgb(220, 38, 38) solid 1px !important;
}
</style>
