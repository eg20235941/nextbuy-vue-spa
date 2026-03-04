<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <h1 class="text-3xl font-bold text-center mb-6">
      NextBuy
    </h1>

    <div v-if="isLoading" class="text-center">
      Loading products...
    </div>

    <div v-else-if="error" class="text-center text-red-500">
      {{ error }}
    </div>

    <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      <div 
        v-for="product in products" 
        :key="product.id"
        class="bg-white p-4 rounded-lg shadow"
      >
        <h2 class="font-semibold text-lg">
          {{ product.title }}
        </h2>
        <p class="text-gray-600">
          ${{ product.price }}
        </p>
      </div>
    </div>
  </div>
</template>


<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { Product, ProductsResponse } from './types/Product'

const products = ref<Product[]>([])
const isLoading = ref<boolean>(false)
const error = ref<string | null>(null)

onMounted(async () => {
  try {
    isLoading.value = true

    const response = await fetch('https://dummyjson.com/products')
    const data: ProductsResponse = await response.json()

    products.value = data.products
  } catch (err) {
    error.value = 'Failed to fetch products'
  } finally {
    isLoading.value = false
  }
})
</script>


<style>
/* Keep empty for now */
</style>
