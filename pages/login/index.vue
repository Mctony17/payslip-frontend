<template>
  <div class="w-full h-screen bg-gray-600">
    <div
      v-if="$nuxt.isOffline"
      class="w-full bg-red-300 text-red-600 text-center px-4 py-2 text-lg font-bold tracking-widest uppercase"
    >
      You are offline. Check your internet connection.
    </div>

    <div class="w-full mx-auto px-4 pt-16 flex">
      <div
        class="w-full sm:max-w-xl mx-auto rounded-lg shadow-xl bg-gray-900 p-4"
      >
        <div class="py-4">
          <h3
            class="text-yellow-500 font-bold uppercase text-2xl tracking-widest"
          >
            Login
          </h3>
        </div>

        <form @submit.prevent="login">
          <div class="">
            <label class="text-gray-500"
              ><span class="text-red-500">*</span>Email</label
            >
            <input
              type="email"
              id="email"
              name="email"
              v-model.trim="$v.email.$model"
              placeholder="Enter your email here"
              autocomplete="given-name"
              aria-describedby="email"
              class="rounded block w-full p-3 mt-2 text-gray-700 bg-gray-200 appearance-none focus:outline-none focus:bg-gray-300 focus:shadow-inner"
            />
            <span
              v-if="!$v.email.required && $v.email.$dirty"
              class="text-red-500"
              >Email is required</span
            >
            <br />
            <label class="text-gray-500"
              ><span class="text-red-500">*</span>Password</label
            >
            <input
              type="password"
              id="password"
              name="password"
              v-model.trim="$v.password.$model"
              placeholder="Enter your password here"
              autocomplete="given-name"
              aria-describedby="password"
              class="rounded block w-full p-3 mt-2 text-gray-700 bg-gray-200 appearance-none focus:outline-none focus:bg-gray-300 focus:shadow-inner"
            />
            <span
              v-if="!$v.password.required && $v.password.$dirty"
              class="text-red-500"
              >Password is required</span
            >
            <br />
            <button
              type="submit"
              class="uppercase text-gray-700 text-lg bg-yellow-500 mt-6 font-bold tracking-widest py-4 px-2 rounded block w-full focus:outline-none hover:bg-yellow-600 hover:text-gray-300"
            >
              {{ loading ? "Please wait..." : "login" }}
            </button>

            <p class="text-gray-400 text-sm mt-4">
              Forgot password?
              <n-link to="/forgot-password"
                ><span class="text-yellow-500 font-semibold"
                  >Click here.</span
                ></n-link
              >
            </p>

            <p class="text-gray-400 text-sm mt-2">
              Have no account?
              <n-link to="/register"
                ><span class="text-yellow-500 font-semibold"
                  >Register here.</span
                ></n-link
              >
            </p>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { required } from "vuelidate/lib/validators";
import Vue from "vue";
import Vuelidate from "vuelidate";
Vue.use(Vuelidate);
export default {
  middleware: "guest",
  data() {
    return {
      email: "",
      password: "",
      loading: false,
    };
  },
  validations: {
    email: {
      required,
    },
    password: {
      required,
    },
  },
  methods: {
    async login() {
      
      this.loading = true;
      this.$v.$touch();
      if (!this.$v.$invalid) {
        try {
          await this.$auth.loginWith("local", {
            data: {
              identifier: this.email,
              password: this.password,
            },
          });
          this.$toast.success("Login successful");
          this.loading = false;
          this.$router.push("/payslip");
          this.email = "";
          this.password = "";
        } catch (e) {
          this.loading = false;
          // put modal here
          this.$toast.error(e.response.data.message[0].messages[0].message);
        }
      } else {
        this.loading = false;
        this.$toast.error("Enter the correct data into the form fields above.");
      }
    },
  },
};
</script>