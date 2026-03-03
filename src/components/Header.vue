<script setup>
const props = defineProps({
  userName: String,
  currentPage: String,
  totalPrice: Number
})

const emit = defineEmits(['open-login', 'logout', 'show-favorites', 'go-back-to-home', 'open-basket'])

const onClickProfile = () => {
  if (props.userName) {
    if (confirm(`Do you want to logout, ${props.userName}?`)) {
      emit('logout')
    }
  } else {
    emit('open-login')
  }
}
</script>

<template>
  <header class="flex flex-col sm:flex-row justify-between items-center border-b border-slate-200 px-4 py-4 sm:px-10 sm:py-8 gap-4">
    
    <div 
      @click="emit('go-back-to-home')" 
      class="flex items-center gap-4 cursor-pointer hover:opacity-80 transition"
    >
      <img src="/logo.png" alt="Logo" class="w-10" />
      <div>
        <h2 class="text-lg sm:text-xl font-bold uppercase leading-none">Vue Sneakers</h2>
        <p class="text-slate-400 text-sm hidden sm:block">Shop the best sneakers</p>
      </div>
    </div>

    <ul class="flex items-center gap-4 sm:gap-10">
      
      <li
        v-if="props.currentPage === 'favorites'"
        @click="emit('go-back-to-home')"
        class="bg-lime-500 text-white rounded-xl hover:bg-lime-600 transition cursor-pointer px-3 py-1.5 sm:px-4 sm:py-2 text-sm sm:text-base"
      >
        Go back
      </li>

      <li @click="emit('open-basket')" class="flex items-center cursor-pointer gap-2 sm:gap-3 text-gray-500 hover:text-black">
        <img src="/cart.svg" alt="Cart" class="w-5 sm:w-6" />
        <b class="text-sm sm:text-base">{{ totalPrice }} Euro</b>
      </li>

      <li 
        v-if="userName"
        @click="emit('show-favorites')"
        class="flex items-center cursor-pointer gap-2 sm:gap-3 text-gray-500 hover:text-black"
      >
        <img src="/heart.svg" alt="Favorite" class="w-5 sm:w-6" />
        <span class="hidden md:inline">Favorites</span>
      </li>

      <li 
        @click="onClickProfile" 
        class="flex items-center cursor-pointer gap-2 sm:gap-3 text-gray-500 hover:text-black"
      >
        <img src="/profile.svg" alt="Profile" class="w-5 sm:w-6" />
        <span class="hidden md:inline">{{ userName ? userName : 'Login' }}</span>
        <span v-if="userName" class="md:hidden text-sm">{{ userName.substring(0, 5) }}..</span>
      </li>
    </ul>
  </header>
</template>