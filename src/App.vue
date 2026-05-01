<template>
  <div
  :class="[
    'min-h-screen transition-colors',
    isDarkMode ? 'bg-slate-950 text-slate-100' : 'bg-slate-50 text-slate-900'
  ]"
>
    <header
  :class="[
    'border-b transition-colors',
    isDarkMode ? 'border-slate-800 bg-slate-900' : 'border-slate-200 bg-white'
  ]"
>
      <div class="mx-auto flex h-16 max-w-6xl items-center justify-between px-4 sm:px-6 lg:px-8">
        <h1 class="text-2xl font-semibold">NextBuy</h1>

        <div class="flex items-center gap-3">
          <nav class="hidden items-center gap-6 text-sm font-medium text-slate-700 md:flex">
            <a href="#" class="transition hover:text-slate-900">Home</a>
            <a href="#products" class="transition hover:text-slate-900">Products</a>
            <a href="#footer" class="transition hover:text-slate-900">About</a>
          </nav>

          <button
  @click="toggleDarkMode"
  class="rounded-xl border border-slate-200 bg-white px-4 py-2 text-sm font-medium text-slate-700 shadow-sm transition hover:bg-slate-50"
>
  {{ isDarkMode ? 'Light' : 'Dark' }}
</button>
          <button
            @click="showCart = true"
            class="hidden rounded-xl border border-slate-200 bg-white px-4 py-2 text-sm font-medium text-slate-700 shadow-sm transition hover:bg-slate-50 md:inline-flex"
          >
            Cart ({{ cartCount }})
          </button>

          <button
            @click="showCart = true"
            class="rounded-xl border border-slate-200 bg-white p-2 text-slate-700 md:hidden"
            aria-label="Open cart"
          >
            🛒
          </button>

          <button
            class="rounded-xl border border-slate-200 bg-white p-2 text-slate-700 md:hidden"
            aria-label="Menu"
          >
            ☰
          </button>
        </div>
      </div>
    </header>

    <main class="mx-auto max-w-6xl px-4 py-10 sm:px-6 lg:px-8">
      <section class="mb-8">
        <h2 class="mb-3 text-3xl font-semibold tracking-tight sm:text-4xl">
          Discover Your Next Buy
        </h2>
        <p class="mt-2 text-sm text-slate-500">
  Browse, search, and explore products easily with a modern shopping experience.
</p>
      </section>

      <section
  :class="[
    'mb-10 rounded-2xl border p-4 shadow-sm transition-colors',
    isDarkMode ? 'border-slate-800 bg-slate-900' : 'border-slate-200 bg-white'
  ]"
>
  <div class="flex flex-col gap-4 md:flex-row md:items-center">
    <input
      v-model="searchQuery"
      type="text"
      placeholder="Search products..."
    :class="[
  'h-12 w-full rounded-xl border px-4 text-sm outline-none transition focus:border-indigo-500',
  isDarkMode ? 'border-slate-700 bg-slate-800 text-slate-100 placeholder:text-slate-400' : 'border-slate-200 bg-slate-50 text-slate-900'
]"
    />

    <select
      v-model="selectedCategory"
      :class="[
  'h-12 w-full rounded-xl border px-4 text-sm capitalize outline-none transition focus:border-indigo-500 md:w-64',
  isDarkMode ? 'border-slate-700 bg-slate-800 text-slate-100' : 'border-slate-200 bg-slate-50 text-slate-900'
]"
    >
      <option value="all">All Categories</option>
      <option v-for="category in categories" :key="category" :value="category">
        {{ category }}
      </option>
    </select>
  </div>
</section>

      <section id="products">
        <div class="mb-6 flex flex-col gap-2 sm:flex-row sm:items-end sm:justify-between">
          <div>
            <h3 class="text-2xl font-semibold">
              {{ resultsHeading }}
            </h3>
            <p class="text-sm text-slate-500">
              {{ resultsSubtext }}
            </p>
          </div>

          <button
            v-if="hasActiveFilter"
            @click="clearFilters"
            class="text-sm font-medium text-indigo-600 transition hover:text-indigo-700"
          >
            Clear Filters
          </button>
        </div>

        <div
  v-if="isLoading"
  :class="[
    'rounded-2xl border p-10 text-center shadow-sm transition-colors',
    isDarkMode ? 'border-slate-800 bg-slate-900 text-slate-400' : 'border-slate-200 bg-white text-slate-500'
  ]"
>
  Loading products...
</div>

<div
  v-else-if="error"
  class="rounded-2xl border border-rose-200 bg-rose-50 p-10 text-center text-rose-600"
>
  {{ error }}
</div>

<template v-else>
  <div
    v-if="visibleProducts.length"
    class="grid grid-cols-1 gap-6 sm:grid-cols-2 xl:grid-cols-4"
  >
    <ProductCard
  v-for="product in visibleProducts"
  :key="product.id"
  :product="product"
  :isDarkMode="isDarkMode"
  @view-product="openProduct"
/>
  </div>

  <div
    v-else
    :class="[
      'rounded-2xl border p-10 text-center shadow-sm transition-colors',
      isDarkMode ? 'border-slate-800 bg-slate-900 text-slate-400' : 'border-slate-200 bg-white text-slate-500'
    ]"
  >
    No matching products found.
  </div>

  <div v-if="canLoadMore" class="mt-8 flex justify-center">
    <button
      @click="loadMore"
      class="inline-flex h-11 items-center justify-center rounded-xl bg-indigo-600 px-5 text-sm font-medium text-white transition hover:bg-indigo-700 hover:shadow-md"
    >
      Load More
    </button>
  </div>
</template>
      </section>
    </main>

    <footer
  id="footer"
  :class="[
    'border-t transition-colors',
    isDarkMode ? 'border-slate-800 bg-slate-900' : 'border-slate-200 bg-white'
  ]"
>
      <div class="mx-auto max-w-6xl px-4 py-6 text-center text-sm text-slate-500 sm:px-6 lg:px-8">
        © 2026 NextBuy. All rights reserved.
      </div>
    </footer>

    <transition name="slide">
      <aside
        v-if="showCart"
        :class="[
  'fixed inset-y-0 right-0 z-40 w-full max-w-md border-l shadow-2xl transition-colors',
  isDarkMode ? 'border-slate-800 bg-slate-900 text-slate-100' : 'border-slate-200 bg-white text-slate-900'
]"
      >
        <div class="flex h-full flex-col">
          <div
  :class="[
    'flex items-center justify-between border-b px-5 py-4',
    isDarkMode ? 'border-slate-800' : 'border-slate-200'
  ]"
>
            <h2 class="text-xl font-semibold">Your Cart</h2>
            <button @click="showCart = false" class="text-2xl leading-none text-slate-400 hover:text-slate-600">×</button>
          </div>

          <div class="flex-1 overflow-y-auto px-5 py-4">
            <div
  v-if="cart.length === 0"
  :class="[
    'rounded-2xl border border-dashed p-8 text-center',
    isDarkMode ? 'border-slate-700 text-slate-400' : 'border-slate-200 text-slate-500'
  ]"
>
              🛒 Your cart is empty
            </div>

            <div v-else class="space-y-4">
              <div
                v-for="item in cart"
                :key="item.id"
                :class="[
  'rounded-2xl border p-4 transition-colors',
  isDarkMode ? 'border-slate-700 bg-slate-800' : 'border-slate-200 bg-slate-50'
]"
              >
                <div class="flex items-start justify-between gap-4">
                  <div>
                    <p class="font-semibold">{{ item.title }}</p>
                    <p class="mt-1 text-sm text-slate-500">${{ item.price }} each</p>
                  </div>
                  <button @click="removeFromCart(item.id)" class="text-sm text-rose-500 hover:text-rose-600">
                    Remove
                  </button>
                </div>

                <div class="mt-4 flex items-center justify-between">
                  <div class="flex items-center gap-3 rounded-xl border border-slate-200 bg-white px-3 py-2">
                    <button @click="decreaseQty(item.id)" class="text-base font-medium">-</button>
                    <span class="text-sm font-medium">{{ item.quantity }}</span>
                    <button @click="increaseQty(item.id)" class="text-base font-medium">+</button>
                  </div>

                  <p class="font-semibold">${{ (item.price * item.quantity).toFixed(2) }}</p>
                </div>
              </div>
            </div>
          </div>

          <div
  :class="[
    'border-t px-5 py-4',
    isDarkMode ? 'border-slate-800' : 'border-slate-200'
  ]"
>
            <div class="mb-4 flex items-center justify-between text-lg font-semibold">
              <span>Total</span>
              <span>${{ cartTotal.toFixed(2) }}</span>
            </div>

            <button
              @click="clearCart"
              class="inline-flex h-11 w-full items-center justify-center rounded-xl bg-rose-500 text-sm font-medium text-white transition hover:bg-rose-600"
            >
              Clear Cart
            </button>
          </div>
        </div>
      </aside>
    </transition>

    <transition name="fade">
      <div
        v-if="selectedProduct"
        @click.self="selectedProduct = null"
        class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 px-4 py-8"
      >
        <div
  :class="[
    'relative w-full max-w-5xl rounded-[24px] p-5 shadow-2xl sm:p-8',
    isDarkMode ? 'bg-slate-900 text-slate-100' : 'bg-white text-slate-900'
  ]"
>
          <button
            @click="selectedProduct = null"
            class="absolute right-5 top-4 text-2xl leading-none text-slate-300 transition hover:text-slate-500"
          >
            ×
          </button>

          <div class="grid gap-8 md:grid-cols-[340px_minmax(0,1fr)] md:items-start">
            <div
  :class="[
    'overflow-hidden rounded-2xl border',
    isDarkMode ? 'border-slate-700 bg-slate-800' : 'border-slate-200 bg-slate-100'
  ]"
>
              <img
                :src="selectedProduct.images?.[0] || selectedProduct.thumbnail"
                :alt="selectedProduct.title"
                class="h-72 w-full object-cover md:h-[340px]"
              />
            </div>

            <div>
              <h3
  :class="[
    'mb-3 text-3xl font-semibold tracking-tight',
    isDarkMode ? 'text-slate-100' : 'text-slate-900'
  ]"
>
  {{ selectedProduct.title }}
</h3>

              <span class="inline-flex rounded-xl bg-slate-100 px-3 py-1 text-sm font-medium capitalize text-slate-700">
                {{ selectedProduct.category }}
              </span>

              <p
  :class="[
    'mt-6 text-4xl font-semibold',
    isDarkMode ? 'text-slate-100' : 'text-slate-900'
  ]"
>
  ${{ selectedProduct.price }}
</p>

              <div class="mt-5 flex items-center gap-2 text-base text-slate-500">
                <span class="text-amber-400">★</span>
                <span>{{ selectedProduct.rating.toFixed(1) }}</span>
              </div>

              <p
  :class="[
    'mt-6 max-w-xl text-base leading-7',
    isDarkMode ? 'text-slate-300' : 'text-slate-500'
  ]"
>
                {{ selectedProduct.description }}
              </p>

              <div class="mt-8 flex flex-wrap gap-3">
                <button
                  @click="addToCart(selectedProduct)"
                  class="inline-flex h-11 items-center justify-center rounded-xl bg-indigo-600 px-6 text-sm font-medium text-white shadow-sm transition hover:bg-indigo-700"
                >
                  Add to Cart
                </button>
                <button
                  @click="selectedProduct = null"
                  class="inline-flex h-11 items-center justify-center rounded-xl border border-slate-200 bg-white px-6 text-sm font-medium text-slate-700 shadow-sm transition hover:bg-slate-50"
                >
                  Back
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref, watch } from 'vue'
import ProductCard from './components/ProductCard.vue'
import type { CartItem, Product, ProductsResponse } from './types/Product'

const products = ref<Product[]>([])
const cart = ref<CartItem[]>([])
const isLoading = ref(false)
const error = ref<string | null>(null)
const searchQuery = ref('')
const selectedCategory = ref('all')
const selectedProduct = ref<Product | null>(null)
const showCart = ref(false)
const displayLimit = ref(8)
const isDarkMode = ref(false)

const categories = computed(() => {
  return [...new Set(products.value.map((product) => product.category))].sort()
})

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    const matchesSearch = product.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    const matchesCategory = selectedCategory.value === 'all' || product.category === selectedCategory.value
    return matchesSearch && matchesCategory
  })
})

const visibleProducts = computed(() => {
  return filteredProducts.value.slice(0, displayLimit.value)
})

const canLoadMore = computed(() => {
  return filteredProducts.value.length > displayLimit.value
})

const hasActiveFilter = computed(() => {
  return searchQuery.value.trim() !== '' || selectedCategory.value !== 'all'
})

const resultsHeading = computed(() => {
  return hasActiveFilter.value ? 'Search Results' : 'Featured Products'
})

const resultsSubtext = computed(() => {
  if (!filteredProducts.value.length) {
    return 'Try another search or category.'
  }

  if (hasActiveFilter.value) {
    return `Showing ${filteredProducts.value.length} matching products`
  }

  return 'Explore trending items across multiple categories'
})

const cartTotal = computed(() => {
  return cart.value.reduce((total, item) => total + item.price * item.quantity, 0)
})

const cartCount = computed(() => {
  return cart.value.reduce((total, item) => total + item.quantity, 0)
})

function openProduct(product: Product) {
  selectedProduct.value = product
}

function clearFilters() {
  searchQuery.value = ''
  selectedCategory.value = 'all'
  displayLimit.value = 8
}

function loadMore() {
  displayLimit.value += 4
}

function addToCart(product: Product) {
  const existingItem = cart.value.find((item) => item.id === product.id)

  if (existingItem) {
    existingItem.quantity += 1
  } else {
    cart.value.push({
      ...product,
      quantity: 1,
    })
  }

  showCart.value = true
}

function removeFromCart(id: number) {
  cart.value = cart.value.filter((item) => item.id !== id)
}

function increaseQty(id: number) {
  const item = cart.value.find((cartItem) => cartItem.id === id)
  if (item) item.quantity += 1
}

function decreaseQty(id: number) {
  const item = cart.value.find((cartItem) => cartItem.id === id)
  if (!item) return

  if (item.quantity > 1) {
    item.quantity -= 1
  } else {
    removeFromCart(id)
  }
}

function clearCart() {
  cart.value = []
}

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value
  localStorage.setItem('darkMode', JSON.stringify(isDarkMode.value))
}

watch([searchQuery, selectedCategory], () => {
  displayLimit.value = 8
})

watch(
  cart,
  (newCart) => {
    localStorage.setItem('cart', JSON.stringify(newCart))
  },
  { deep: true },
)

onMounted(async () => {
  try {
    isLoading.value = true

    const response = await fetch('https://dummyjson.com/products?limit=100')
    const data: ProductsResponse = await response.json()

    products.value = data.products
  } catch {
    error.value = 'Failed to fetch products.'
  } finally {
    isLoading.value = false
  }

  const savedCart = localStorage.getItem('cart')
  if (savedCart) {
    cart.value = JSON.parse(savedCart)
  }
  const savedDarkMode = localStorage.getItem('darkMode')
if (savedDarkMode) {
  isDarkMode.value = JSON.parse(savedDarkMode)
}
})
</script>

<style>
.slide-enter-from,
.slide-leave-to {
  transform: translateX(100%);
}

.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: scale(0.96);
}

.fade-enter-active,
.fade-leave-active {
  transition: all 0.25s ease;
}
</style>
