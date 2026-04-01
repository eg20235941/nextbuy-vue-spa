<script setup lang="ts">
import type { Product, CartItem } from '../types/Product'

const props = defineProps<{
  product: Product
  cart: CartItem[]
}>()

const emit = defineEmits(['add-to-cart', 'view-product'])
</script>

<template>
  <div
    class="border rounded p-4 shadow cursor-pointer hover:scale-105 transition duration-300"
    @click="emit('view-product', props.product)"
  >

    <img
      :src="props.product.image"
      class="h-40 w-full object-contain mb-3"
    />

    <h2 class="font-semibold text-lg mb-2">
      {{ props.product.title }}
    </h2>

    <p class="text-gray-500 text-sm mb-2">
      {{ props.product.category }}
    </p>

    <p class="font-bold text-blue-600 mb-3">
      ${{ props.product.price }}
    </p>

    <div class="flex items-center mb-2">

  <span class="text-yellow-400 text-lg">
    {{ '★'.repeat(Math.round(product.rating)) }}
  </span>

  <span class="text-gray-400 text-lg">
    {{ '☆'.repeat(5 - Math.round(product.rating)) }}
  </span>

  <span class="text-sm text-gray-500 ml-2">
    ({{ product.rating }})
  </span>

</div>

    <button
      @click.stop="emit('add-to-cart', props.product)"
      :disabled="props.cart.some(p => p.id === props.product.id)"
      class="bg-blue-500 text-white px-4 py-2 rounded disabled:opacity-50"
    >
      Add to Cart
    </button>

  </div>
</template>

