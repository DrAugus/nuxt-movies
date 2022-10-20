<script setup lang="ts">
import type { MediaType } from '~/types'
import { QUERY_LIST, QUERY_MARVEL_LIST } from '~/constants/lists'

const route = useRoute()
const type = $computed(() => route.params.type as MediaType || 'movie')

const queries = $computed(() => [
  QUERY_LIST.movie[0],
  QUERY_LIST.tv[0],
])

const queriesMarvel = $computed(() => [
  QUERY_MARVEL_LIST.movie[0],
  QUERY_MARVEL_LIST.tv[0],
])

const AsyncWrapper = defineComponent(async (_, ctx) => {
  const list = await listMedia(type, queries[0].query, 1)
  const item = await getMedia(type, list.results[0].id)
  return () => ctx.slots?.default?.({ item })
})

const AsyncMarvelWrapper = defineComponent(async (_, ctx) => {
  const list = await listMarvelMedia(type, queriesMarvel[0].query, 1)
  console.log('augus list: ',list)
  const item = await getMedia(type, list.results[0].id)
  return () => ctx.slots?.default?.({ item })
})
</script>

<template>
  <div>
    <AsyncMarvelWrapper>
      <template #default="{ item }">
        <NuxtLink :to="`/${type}/${item.id}`">
          <MediaHero :item="item" />
        </NuxtLink>
      </template>
    </AsyncMarvelWrapper>
    <CarouselAutoQuery
      v-for="query of queriesMarvel"
      :key="query.type + query.query"
      :query="query"
    />
    <TheFooter />
  </div>
</template>
