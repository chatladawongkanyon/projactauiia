<template>
<div class="flex flex-col items-center min-h-screen bg-white p-4">
<!-- ส่วนหัว -->
<div class="w-full bg-blue-700 py-6 flex justify-center fixed top-0 left-0 right-0 z-50 rounded-b-xl">
<h2 class="text-white text-3xl font-bold tracking-wider uppercase">
        AUIIA Car Parking Systems
</h2>
</div>
 
    <!-- กล่องเข้าสู่ระบบ -->
<div
      class="w-full max-w-lg p-10 rounded-xl shadow-lg mt-32"
      style="background: linear-gradient(135deg, #5a60ff, #ff60c8);"
>
<h2 class="text-center text-white text-3xl font-bold mb-6">LOGIN</h2>
<form @submit.prevent="login" class="space-y-6">
<div>
<label class="block text-white text-sm font-medium">EMAIL:</label>
<input
            v-model="username"
            type="text"
            class="w-full p-3 mt-1 rounded-md border border-gray-300 focus:border-white focus:ring-white outline-none"
          />
</div>
<div>
<label class="block text-white text-sm font-medium">PASSWORD:</label>
<input
            v-model="password"
            type="password"
            class="w-full p-3 mt-1 rounded-md border border-gray-300 focus:border-white focus:ring-white outline-none"
          />
</div>
<button
          type="submit"
          class="w-full bg-white text-gray-800 py-3 rounded-md font-bold hover:opacity-90 transition"
>
          Login
</button>
<p v-if="showWarning" class="text-red-500 text-sm mt-2 text-center">
          Please enter both email and password.
</p>
</form>
<div class="text-center mt-6">
<button
          @click="openForgotPasswordModal"
          class="text-white text-sm underline hover:opacity-80"
>
          FORGOT PASSWORD
</button>
</div>
</div>
</div>
</template>
 
<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import { getAuth, signInWithEmailAndPassword } from "firebase/auth";
 
const router = useRouter();
const username = ref("");
const password = ref("");
const showWarning = ref(false);
 
function login() {
  const auth = getAuth();
  if (!username.value || !password.value) {
    showWarning.value = true;
    return;
  }
  signInWithEmailAndPassword(auth, username.value, password.value)
    .then(() => {
      router.push("/dashboard");
    })
    .catch(() => {
      showWarning.value = true;
    });
}
 
function openForgotPasswordModal() {
  router.push("/forgot-password");
}
</script>
 
<style scoped>
/* ส่วนหัว */
h2 {
  font-size: 1.5rem;
  font-weight: bold;
  text-align: center;
  width: 100%;
}
 
/* กล่องเข้าสู่ระบบ */
input[type="text"],
input[type="password"] {
  background: white;
  color: black;
  font-size: 1.1rem;
  padding: 0.75rem;
}
</style>
