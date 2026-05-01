<script setup lang="ts">
import type { Product } from '../types/Product'

const props = defineProps<{
  product: Product
  isDarkMode: boolean
}>()

const emit = defineEmits<{
  (e: 'view-product', product: Product): void
}>()
</script>

<template>
  <article
  :class="[
    'group rounded-2xl border p-4 shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-xl cursor-pointer',
    props.isDarkMode ? 'border-slate-800 bg-slate-900' : 'border-slate-200 bg-white'
  ]"
>
    <div class="h-36 w-full object-cover sm:h-44 transition duration-300 group-hover:scale-105">
      <img
        :src="props.product.thumbnail"
        :alt="props.product.title"
        class="h-36 w-full object-cover sm:h-44"
      />
    </div>

    <h3
  :class="[
    'mb-1 line-clamp-2 text-lg font-semibold',
    props.isDarkMode ? 'text-slate-100' : 'text-slate-900'
  ]"
>
      {{ props.product.title }}
    </h3>

    <p
  :class="[
    'mb-2 text-sm capitalize',
    props.isDarkMode ? 'text-slate-400' : 'text-slate-500'
  ]"
>
      {{ props.product.category }}
    </p>

    <p
  :class="[
    'mb-2 text-2xl font-semibold',
    props.isDarkMode ? 'text-slate-100' : 'text-slate-900'
  ]"
>
      ${{ props.product.price }}
    </p>

    <div class="mb-4 flex items-center gap-2 text-sm text-slate-500">
      <span class="text-amber-400">★</span>
      <span>{{ props.product.rating.toFixed(1) }}</span>
    </div>

    <button
  @click.stop="emit('view-product', props.product)"
      class="inline-flex h-10 items-center justify-center rounded-xl bg-indigo-600 px-4 text-sm font-medium text-white transition hover:bg-indigo-700 hover:shadow-md"
    >
      View Details
    </button>
  </article>
</template>
