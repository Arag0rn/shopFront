<script setup>
import Header from './components/Header.vue'
import Card from './components/Card.vue';
import axios from './axios' 
import Login from './components/Login.vue'
import Basket from './components/Basket.vue'
import { ref, onMounted, computed, watch } from 'vue'

const isLoginOpen = ref(false) 
const userData = ref(null)
const items = ref([]);
const favorites = ref([]);
const currentPage = ref('home');
const cart = ref(JSON.parse(localStorage.getItem('cart') || '[]'));
const isBasketOpen = ref(false)

const openBasket = () => (isBasketOpen.value = true, console.log('Basket opened'))
const closeBasket = () => (isBasketOpen.value = false)

const handleLoginSuccess = async (data) => {
  userData.value = data
  isLoginOpen.value = false 
  const { data: serverCart } = await axios.get('/cart');
  
  if (serverCart.length > 0) {
    const localCart = cart.value;
    const combined = [...localCart];
    
    serverCart.forEach(sItem => {
      if (!combined.find(lItem => lItem._id === sItem._id)) {
        combined.push(sItem);
      }
    });
    cart.value = combined;
  }
}

let timeoutId = null;

watch(cart, (newCart) => {
  localStorage.setItem('cart', JSON.stringify(newCart));

  if (userData.value) {
    if (timeoutId) clearTimeout(timeoutId);

    timeoutId = setTimeout(async () => {
      try {
        await axios.put('/cart', { 
          items: newCart.map(item => ({ item: item._id })) 
        });
        console.log('Cart synced with server');
      } catch (err) {
        console.error('Failed to sync cart');
      }
    }, 500); 
  }
}, { deep: true });

const handleLogout = () => {
  if (confirm('Do you really want to logout?')) {
    window.localStorage.removeItem('token');
    userData.value = null;
  }
}

onMounted(async () => {
  const token = window.localStorage.getItem('token');
  if (token) {
    try {
      const { data } = await axios.get('/auth/me');
      userData.value = data; 
    } catch (err) {
      console.warn('Token expired or invalid');
      window.localStorage.removeItem('token');
      userData.value = null;
    }
  }
});

onMounted(async () => {
  try {
    const { data } = await axios.get('/items'); 
    items.value = data;
  } catch (err) {
    console.log('Error fetching items:', err);
  }
  await fetchFavorites();

});

const fetchFavorites = async () => {
  try {
    const { data: favsData } = await axios.get('/favorites');
    favorites.value = favsData.map(f => f.item._id);
  } catch (err) {
    console.log('Error fetching favorites:', err);
  }
}

  const onToggleFavorite = async (item) => {
  if (!userData.value) return alert('Please login first!');
  
  try {
    const { data } = await axios.post('/favorites', { itemId: item._id });
    if (data.added) {
      favorites.value.push(item._id);
    } else {
      favorites.value = favorites.value.filter(id => id !== item._id);
    }
  } catch (err) {
    console.error(err);
  }
}

const toggleCart = (item) => {
  const index = cart.value.findIndex((cartItem) => cartItem._id === item._id);
  
  if (index === -1) {
    cart.value.push(item);
  } else {
    cart.value = cart.value.filter((cartItem) => cartItem._id !== item._id);
  }
};

const openFavorites = () => {
  currentPage.value = 'favorites';
}

const goBackToHome = () => {
  currentPage.value = 'home';
}

const displayedItems = computed(() => {
  if (currentPage.value === 'favorites') {
    return items.value.filter(item => favorites.value.includes(item._id))
  }
  return items.value
})

const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.price, 0);
});

</script>

<template>
  <Transition name="slide">
    <Basket 
      v-if="isBasketOpen" 
      :items="cart" 
      :total="totalPrice" 
      @close="closeBasket"
      @remove="toggleCart"
      

    />
  </Transition>
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 mb-14 pb-14">
    <Header :user-name="userData?.fullName" 
    :total-price="totalPrice"
    @open-login="isLoginOpen = true" 
    @logout="handleLogout"
    @show-favorites="openFavorites"
    @go-back-to-home="goBackToHome"
    @open-basket="openBasket"
    :current-page="currentPage"

    />

    <h2 class="text-center text-2xl font-bold mt-10">{{ currentPage === 'favorites' ? 'Favorite Products' : 'Our Products' }}</h2>
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-5 mt-10 px-8 min-h-[85vh] items-start content-start">

      <template v-if="displayedItems.length > 0">
        <Card 
          v-for="item in displayedItems" 
          :key="item._id" 
          :image="item.imageUrl" 
          :title="item.title" 
          :price="item.price" 
          :is-favorite="favorites.includes(item._id)"
          :is-added="cart.some(c => c._id === item._id)"
          @toggle-favorite="onToggleFavorite(item)"
          @add-to-cart="toggleCart(item)"
        />
      </template>
  
    <div v-else class="col-span-full flex flex-col items-center justify-center py-20">
        <img src="/emoji-1.png" alt="Empty" class="w-20 h-20 mb-4 opacity-50" />
        <h3 class="text-xl font-bold text-slate-400">No products found</h3>
      </div>
    </div>

    <Login 
      v-if="isLoginOpen" 
      @close="isLoginOpen = false" 
      @login-success="handleLoginSuccess"
    
    />
  </div>
</template>

<style scoped>
  </style>