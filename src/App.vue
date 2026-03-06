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
      <input
  v-model="searchQuery"
  type="text"
  placeholder="Search products..."
  class="border p-2 mb-4 w-full rounded"
/>
<div class="flex gap-2 mb-4 flex-wrap">
  <button @click="selectedCategory = 'all'" class="px-3 py-1 bg-gray-200 rounded">
    All
  </button>

  <button @click="selectedCategory = 'electronics'" class="px-3 py-1 bg-gray-200 rounded">
    Electronics
  </button>

  <button @click="selectedCategory = 'jewelery'" class="px-3 py-1 bg-gray-200 rounded">
    Jewelery
  </button>

 <button @click="selectedCategory = 'men\'s clothing'" class="px-3 py-1 bg-gray-200 rounded">
  Men's
</button>

<button @click="selectedCategory = 'women\'s clothing'" class="px-3 py-1 bg-gray-200 rounded">
  Women's
</button>

</div>

      
      <ProductCard
  v-for="product in filteredProducts"
  :key="product.id"
  :product="product"
/>

    </div>
  </div>
</template>


<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import type { Product, ProductsResponse } from './types/Product'
import ProductCard from './components/ProductCard.vue'


const products = ref<Product[]>([])
const isLoading = ref<boolean>(false)
const error = ref<string | null>(null)
const searchQuery = ref('')
const selectedCategory = ref('all')




const filteredProducts = computed(() => {
  return products.value.filter(product => {
    const matchesSearch = product.title
      .toLowerCase()
      .includes(searchQuery.value.toLowerCase())

    const matchesCategory =
      selectedCategory.value === 'all' ||
      product.category === selectedCategory.value

    return matchesSearch && matchesCategory
  })
})


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
