<script setup>
import { ref } from 'vue'
const props = defineProps({
  image: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean
})
const emit = defineEmits(['toggle-favorite'])

const handleHeartClick = () => {
  emit('toggle-favorite')

}

const handlePlusClick = () => {
  emit('add-to-cart') 
}
</script>
<template>
  <div class="relative flex flex-col justify-between h-full m-2 border border-slate-300 rounded-3xl p-6 cursor-pointer hover:shadow-xl hover:-translate-y-2 transition-all duration-300 bg-white">
    
    <div>
      <img 
        @click="handleHeartClick" 
        :src="isFavorite ? '/like-2.svg' : '/like-1.svg'" 
        alt="Like Icon"
        class="absolute top-6 left-6 z-10" 
      />
      
      <div class="flex justify-center items-center h-40 mb-4">
        <img :src="image" :alt="title" class="max-h-full object-contain">
      </div>
      
      <p class="text-sm font-medium mb-4 line-clamp-2 h-10">{{ title }}</p>
    </div>

    <div class="flex justify-between items-end mt-auto">
      <div class="flex flex-col">
        <span class="text-slate-400 text-[10px] font-bold uppercase">Price:</span>
        <b class="text-sm">{{ price }} Euro</b>
      </div>
      <img @click="handlePlusClick" 
        :src="isAdded ? '/checked.svg' : '/plus.svg'" 
        :alt="isAdded ? 'Checked Icon' : 'Plus Icon'" 
        class="w-8 h-8 opacity-80 hover:opacity-100" />
    </div>
  </div>
</template>