<template>
  <div class="min-h-screen bg-gray-100 p-6">

    <!-- Header -->
    <div class="flex justify-between items-center mb-6">
      <h1 class="text-3xl font-bold">
        NextBuy
      </h1>

      <button
        @click="showCart = !showCart"
        class="bg-blue-500 text-white px-4 py-2 rounded"
      >
        Cart ({{ cart.length }})
      </button>
    </div>

    <!-- Loading -->
    <div v-if="isLoading" class="text-center">
      Loading products...
    </div>

    <!-- Error -->
    <div v-else-if="error" class="text-center text-red-500">
      {{ error }}
    </div>

    <!-- Products -->
    <div v-else>

      <!-- Search -->
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search products..."
        class="border p-2 mb-4 w-full rounded"
      />

      <!-- Category Buttons -->
      <div class="flex gap-2 mb-4 flex-wrap">
        <button @click="selectedCategory = 'all'" class="px-3 py-1 bg-gray-200 rounded">All</button>
        <button @click="selectedCategory = 'smartphones'" class="px-3 py-1 bg-gray-200 rounded">Electronics</button>
        <button @click="selectedCategory = 'laptops'" class="px-3 py-1 bg-gray-200 rounded">Jewelery</button>
        <button @click="selectedCategory = 'fragrances'" class="px-3 py-1 bg-gray-200 rounded">Men's</button>
        <button @click="selectedCategory = 'skincare'" class="px-3 py-1 bg-gray-200 rounded">Women's</button>
      </div>

      <!-- Product Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
       <ProductCard
  v-for="product in filteredProducts"
  :key="product.id"
  :product="product"
  :cart="cart"
  @add-to-cart="addToCart"
/>


      </div>

    </div>

    <!-- Cart Panel -->
   <transition name="slide">
  <div v-if="showCart">
    <div
      class="fixed top-0 right-0 w-80 h-full bg-white shadow-lg p-4 overflow-y-auto"
    >



      <h2 class="text-xl font-bold mb-4">Your Cart</h2>

      <div v-if="cart.length === 0" class="text-center text-gray-500 mt-10">
  🛒 Your cart is empty
</div>

      <div v-for="item in cart" :key="item.id" class="mb-4 border-b pb-2">

        <p class="font-semibold">
          {{ item.title }}
        </p>

        <div class="flex items-center gap-2 mt-2">

  <button
    @click="decreaseQty(item.id)"
    class="px-2 bg-gray-200 rounded"
  >
    -
  </button>

  <span>{{ item.quantity }}</span>

  <button
    @click="increaseQty(item.id)"
    class="px-2 bg-gray-200 rounded"
  >
    +
  </button>

  </div>




        <p class="font-bold">
  ${{ (item.price * item.quantity).toFixed(2) }}
</p>


        <button
          @click="removeFromCart(item.id)"
          class="text-red-500 text-sm"
        >
          Remove
        </button>

      </div>

      <div class="mt-4 font-bold text-lg">
  Total: ${{ cartTotal.toFixed(2) }}
</div>

<button
  @click="cart = []"
  class="bg-red-500 text-white px-4 py-2 rounded mt-4 w-full"
>
  Clear Cart
</button>

</div>
</div>
</transition>

<transition name="fade">
  <div
    v-if="selectedProduct"
    @click.self="selectedProduct = null"
    class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
  >

    <div class="bg-white p-6 rounded w-96">

      <img
        :src="selectedProduct?.image?.[0] || selectedProduct?.thumbnail"
        class="w-full h-48 object-cover mb-4"
      />

      <h2 class="text-xl font-bold mb-2">
        {{ selectedProduct?.title }}
      </h2>

      <p class="text-gray-600 mb-4">
        {{ selectedProduct?.description }}
      </p>

      <p class="font-bold mb-4">
        ${{ selectedProduct?.price }}
      </p>

      <div class="flex justify-between">

        <button
          @click="addToCart(selectedProduct!)"
          class="bg-blue-500 text-white px-4 py-2 rounded"
        >
          Add to Cart
        </button>

        <button
          @click="selectedProduct = null"
          class="text-gray-500"
        >
          Close
        </button>

      </div>

    </div>

  </div>
</transition>


  </div>
</template>



<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'
import type { Product, ProductsResponse } from './types/Product'
import ProductCard from './components/ProductCard.vue'
import type { CartItem } from './types/Product'


const cart = ref<CartItem[]>([])
const showCart = ref(false)




const products = ref<Product[]>([])
const isLoading = ref<boolean>(false)
const error = ref<string | null>(null)
const searchQuery = ref('')
const selectedCategory = ref('all')
const selectedProduct = ref<Product | null>(null)






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
  const savedCart = localStorage.getItem('cart')

if (savedCart) {
  cart.value = JSON.parse(savedCart)
}

})

function addToCart(product: Product) {

  const existing = cart.value.find(p => p.id === product.id)

  if (existing) {
    existing.quantity++
  } else {
    cart.value.push({
      ...product,
      quantity: 1
    })
  }

}

function removeFromCart(id: number) {
  cart.value = cart.value.filter(item => item.id !== id)
}
const cartTotal = computed(() => {
  return cart.value.reduce((total, item) => {
    return total + item.price * item.quantity
  }, 0)
  
})
watch(cart, (newCart) => {
  localStorage.setItem('cart', JSON.stringify(newCart))
}, { deep: true })

function increaseQty(id: number) {
  const item = cart.value.find(i => i.id === id)
  if (item) item.quantity++
}

function decreaseQty(id: number) {
  const item = cart.value.find(i => i.id === id)
  if (item && item.quantity > 1) {
    item.quantity--
  }
}


</script>


<style>
/* CART SLIDE */
.slide-enter-from {
  transform: translateX(100%);
}
.slide-enter-to {
  transform: translateX(0);
}
.slide-leave-from {
  transform: translateX(0);
}
.slide-leave-to {
  transform: translateX(100%);
}
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease;
}

/* MODAL FADE + SCALE */
.fade-enter-from {
  opacity: 0;
  transform: scale(0.9);
}
.fade-enter-to {
  opacity: 1;
  transform: scale(1);
}
.fade-leave-from {
  opacity: 1;
  transform: scale(1);
}
.fade-leave-to {
  opacity: 0;
  transform: scale(0.9);
}
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}

</style>
