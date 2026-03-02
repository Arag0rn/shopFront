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
  <header class="flex justify-between border-b border-slate-200 px-10 py-8">
    <div class="flex items-center gap-4">
      <img src="/logo.png" alt="Logo" class="w-10" />
      <div>
        <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
        <p class="text-slate-400">Shop the best sneakers</p>
      </div>
    </div>

    <ul class="flex items-center gap-10">

      <li
      v-if="props.currentPage === 'favorites'"
      @click="emit('go-back-to-home')"
      class=" bg-lime-500 text-white rounded-xl hover:bg-lime-600 transition cursor-pointer px-4 py-2"
    >
      Go back to shop
      </li>
      <li @click="emit('open-basket')" class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
        <img src="/cart.svg" alt="Cart" />
        <b> {{ totalPrice }} Euro</b>
      </li>

      <li 
        v-if="userName"
        class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black"
      >
        <img src="/heart.svg" alt="Favorite" />
        <span @click="emit('show-favorites')">Favorites</span>
      </li>

      <li 
        @click="onClickProfile" 
        class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black"
      >
        <img src="/profile.svg" alt="Profile" />
        <span>{{ userName ? userName : 'Login' }}</span>
      </li>
    </ul>
  </header>
</template>
