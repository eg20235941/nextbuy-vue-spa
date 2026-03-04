<template>
  <div class="bg-white p-4 rounded-lg shadow hover:shadow-lg transition">
    <h2 class="font-semibold text-lg">
      {{ product.title }}
    </h2>
    <p class="text-gray-600">
      ${{ product.price }}
    </p>
  </div>
</template>

<script setup lang="ts">
import type { Product } from '../types/Product'

defineProps<{
  product: Product
}>()
</script>
