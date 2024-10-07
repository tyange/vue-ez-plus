<script setup lang="ts">
import { ref, onMounted } from 'vue'

const images = ref<
  {
    id: string
    author: string
    width: number
    height: number
    url: string
    download_url: string
  }[]
>([])

async function fetchImages() {
  try {
    const res = await fetch('https://picsum.photos/v2/list')
    const data = await res.json()

    images.value = data
  } catch (err) {
    console.log(err)
  }
}

onMounted(() => {
  fetchImages()
})
</script>

<template>
  <main>
    <div class="big-image" v-if="images.length > 0">
      <img :src="images[0].download_url" />
    </div>
    <div>
      <ul class="list">
        <li class="item" v-for="image in images" :key="image.id">
          <img :src="image.download_url" />
        </li>
      </ul>
    </div>
  </main>
</template>

<style scoped>
.big-image {
  width: fit-content;
}
.big-image > img {
  width: 50%;
  height: 50%;
}
.list {
  display: grid;
  list-style: none;
  grid-template-columns: repeat(6, 1fr);
  gap: 1rem;
}
.item {
  width: 4rem;
  height: 4rem;
}
.item > img {
  width: 100%;
  height: 100%;
}
</style>
