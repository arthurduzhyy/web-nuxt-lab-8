<template>
  <div class="bg-gray-950 rounded-2xl m-10">
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Filter people..." />
    </div>

    <UTable
        :loading="pending"
        :rows="rows"
        :columns="columns"
        :loading-state="{ icon: 'i-heroicons-arrow-path-20-solid', label: 'Loading...' }"
        :progress="{ color: 'primary', animation: 'carousel' }"
        class="w-full"
    >
      <template #rating-data="{ row }">
        <span :class="{ 'text-green-500': row.rating >= 4.5, 'text-red-500': row.rating < 4.5 }">{{ row.rating }}</span>
      </template>
      <template #thumbnail-data="{ row }">
        <img :src="row.thumbnail" alt="Thumbnail" width="100" height="100" />
      </template>
    </UTable>

    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="filteredRows.length" />
    </div>
  </div>
</template>
<script setup lang="ts">
const pageTitle = ref('Список товарів')

useHead(() => {
  return {
    title: pageTitle.value
  }
})

interface Product {
  id: number
  title: string
  description: string
  price: number
  rating: number
  brand: string
  category: string
  thumbnail: string
}

const columns = [
  {
    key: 'title',
    label: 'Title',
    sortable: true
  },
  {
    key: 'description',
    label: 'Description',
    sortable: true
  },
  {
    key: 'price',
    label: 'Price',
    sortable: true
  },
  {
    key: 'rating',
    label: 'Rating',
    sortable: true,
    color: 'ratingColor'
  },
  {
    key: 'brand',
    label: 'Brand',
    sortable: true
  },
  {
    key: 'category',
    label: 'Category',
    sortable: true
  },
  {
    key: 'thumbnail',
    label: 'Thumbnail'
  }
]

const { data, pending } = await useLazyAsyncData<Product>('products', () => $fetch('https://dummyjson.com/products'))

const products = data.value.products

const q = ref('')

const filteredRows = computed(() => {
  if (!q.value)
    return products

  return products.filter((p: Product) => {
    return Object.values(p).some((value: any) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})

const page = ref(1)
const pageCount = 5

const rows = computed(() => {
  return filteredRows.value.slice((page.value - 1) * pageCount, page.value * pageCount)
})
</script>