<script setup>
import { ref, computed } from 'vue'
import { useMouseInElement } from '@vueuse/core'

const props = defineProps({
  image: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean
})

const emit = defineEmits(['toggle-favorite', 'add-to-cart'])

const target = ref(null)

const { elementX, elementY, elementWidth, elementHeight, isOutside } = useMouseInElement(target)

const cardStyle = computed(() => {
  if (isOutside.value) {
    return { transform: 'rotateX(0deg) rotateY(0deg)' }
  }

  const centerX = elementWidth.value / 2
  const centerY = elementHeight.value / 2

  const rotateX = ((elementY.value - centerY) / centerY) * -15 
  const rotateY = ((elementX.value - centerX) / centerX) * 15

  return {
    transform: `rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale(1.05)`
  }
})

const handleHeartClick = () => emit('toggle-favorite')
const handlePlusClick = () => emit('add-to-cart')
</script>

<template>
  <div ref="target" class="perspective-container">
    <div 
      class="card-body relative flex flex-col justify-between h-full m-2 border border-slate-300 rounded-3xl p-6 cursor-pointer bg-white transition-transform duration-150 ease-out"
      :style="cardStyle"
    >
      <div>
        <img 
          @click.stop="handleHeartClick" 
          :src="isFavorite ? '/like-2.svg' : '/like-1.svg'" 
          alt="Like"
          class="absolute top-6 left-6 z-10" 
        />
        
        <div class="flex justify-center items-center h-40 mb-4">
          <img :src="image" :alt="title" class="max-h-full object-contain pointer-events-none">
        </div>
        
        <p class="text-sm font-medium mb-4 line-clamp-2 h-10">{{ title }}</p>
      </div>

      <div class="flex justify-between items-end mt-auto">
        <div class="flex flex-col">
          <span class="text-slate-400 text-[10px] font-bold uppercase">Price:</span>
          <b class="text-sm">{{ price }} Euro</b>
        </div>
        <img @click.stop="handlePlusClick" 
          :src="isAdded ? '/checked.svg' : '/plus.svg'" 
          class="w-8 h-8 opacity-80 hover:opacity-100" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.perspective-container {
  perspective: 1000px;
  height: 100%;
}

.card-body {
  transform-style: preserve-3d;
  will-change: transform;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
}

.card-body:hover {
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.12);
}
</style>