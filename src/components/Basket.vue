
<script setup>
import { onMounted, onUnmounted } from 'vue'
defineProps({
  items: Array,
  total: Number
})
defineEmits(['close', 'remove'])

onMounted(() => {
  document.body.style.overflow = 'hidden'
})
onUnmounted(() => {
  document.body.style.overflow = 'auto'
})
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full z-20">
    <div @click="$emit('close')" class="absolute top-0 left-0 h-full w-full bg-black opacity-70"></div>

    <div class="absolute right-0 top-0 h-full w-96 bg-white p-8 flex flex-col shadow-xl">
      
      <div class="flex items-center gap-5 mb-8">
        <img 
          @click="$emit('close')" 
          src="/close.svg" 
          class="absolute top-4 right-4 cursor-pointer transition hover:rotate-90" 
        />
        <h2 class="text-2xl font-bold">Basket</h2>
      </div>

      <div class="grow overflow-y-auto">
        <div v-if="items.length > 0">
          <div 
            v-for="item in items" 
            :key="item._id" 
            class="relative cart-item flex items-center border border-slate-200 p-4 rounded-xl gap-4 mb-4 transition-colors"
          >
            <img :src="item.imageUrl" :alt="item.title" class="w-20 h-20 object-contain" />
            
            <div class="flex flex-col grow">
              <p class="text-sm">{{ item.title }}</p>
              <div class="flex justify-between items-center mt-2">
                <b class="text-sm">{{ item.price }} Euro</b>
                <img 
                  @click="$emit('remove', item)"
                  src="/close.svg" 
                  class="close-btn absolute top-2 right-2 cursor-pointer transition w-5 h-5" 
                />
              </div>
            </div>
          </div>
        </div>

        <div v-else class="flex flex-col items-center justify-center h-full opacity-50">
          <img src="/emoji-1.png" alt="Empty" class="w-20 h-20 mb-4" />
          <h3 class="text-xl font-bold">Basket is empty</h3>
          <p class="text-center mt-2 text-sm">Add at least one pair of sneakers to place an order.</p>
        </div>
      </div>

      <div v-if="items.length > 0" class="mt-8">
        <div class="flex items-end gap-2 mb-4">
          <span class="text-slate-500">Summary:</span>
          <div class="flex-grow border-b border-dashed border-slate-300 mb-1"></div>
          <b class="text-xl">{{ total }} Euro</b>
        </div>

        <button 
          class="w-full bg-lime-500 text-white rounded-xl py-3 font-bold hover:bg-lime-600 active:bg-lime-700 transition"
        >
            Order Now
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.cart-item:has(.close-btn:hover) {
  background-color: #fef2f2; 
  border-color: #fecaca;  
}
</style>