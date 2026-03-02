<script setup>
import { ref } from 'vue';
import axios from '../axios';

const isLogin = ref(true);
const email = ref('');
const password = ref('');
const fullName = ref(''); 

const emit = defineEmits(['close', 'login-success']);

const handleSubmit = async () => {
  try {
    const url = isLogin.value ? '/auth/login' : '/auth/register';
    
    const payload = {
      email: email.value,
      password: password.value,
      ...(isLogin.value ? {} : { fullName: fullName.value })
    };

    const { data } = await axios.post(url, payload);

    if (data.token) {
      window.localStorage.setItem('token', data.token);
      emit('login-success', data);
      alert(isLogin.value ? `Welcome back!` : `Registration successful!`);
    }
  } catch (err) {
    alert('Error: ' + (err.response?.data?.message || 'Action failed'));
  }
};
</script>

<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white p-8 rounded-2xl shadow-xl w-96 relative">
      <button @click="$emit('close')" class="absolute top-4 right-4 text-gray-400 hover:text-black">✕</button>
      
      <h2 class="text-2xl font-bold mb-6 text-center">
        {{ isLogin ? 'Sign In' : 'Create Account' }}
      </h2>
      
      <div class="flex flex-col gap-4">
        <input 
          v-if="!isLogin"
          v-model="fullName" 
          type="text" 
          placeholder="Full Name" 
          class="border border-gray-200 p-3 rounded-xl focus:outline-none focus:border-slate-400"
        />
        
        <input v-model="email" type="email" placeholder="Email" class="border border-gray-200 p-3 rounded-xl focus:outline-none focus:border-slate-400" />
        <input v-model="password" type="password" placeholder="Password" class="border border-gray-200 p-3 rounded-xl focus:outline-none focus:border-slate-400" />
        
        <button @click="handleSubmit" class="bg-lime-500 w-full rounded-xl py-3 text-white font-bold hover:bg-lime-600 transition">
          {{ isLogin ? 'Login' : 'Register' }}
        </button>

        <p @click="isLogin = !isLogin" class="text-center text-sm text-slate-400 cursor-pointer hover:text-slate-600">
          {{ isLogin ? "Don't have an account? Register" : "Already have an account? Login" }}
        </p>
      </div>
    </div>
  </div>
</template>