<script setup lang="ts">
import { ref, onMounted, useTemplateRef } from 'vue'

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

const squareElement = useTemplateRef('square-element')
const bigImageElement = useTemplateRef('big-image')

const update = (e: MouseEvent) => {
  if (!squareElement.value || !bigImageElement.value) {
    return
  }

  const rect = bigImageElement.value.getBoundingClientRect()
  const squareSize = 50 // 네모의 크기

  // 마우스 위치를 big-image 요소 내부로 제한
  const x = Math.min(Math.max(e.clientX - rect.left, squareSize / 2), rect.width - squareSize / 2)
  const y = Math.min(Math.max(e.clientY - rect.top, squareSize / 2), rect.height - squareSize / 2)

  squareElement.value.style.left = `${x}px`
  squareElement.value.style.top = `${y}px`
}

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
    <div class="big-image" v-if="images.length > 0" @mousemove="update" ref="big-image">
      <img :src="images[0].download_url" />
      <div class="follower" ref="square-element"></div>
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
  position: relative;
  width: 30rem;
  overflow: hidden;
}
.big-image > img {
  width: 100%;
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
.follower {
  position: absolute;
  width: 50px;
  height: 50px;
  background-color: rgba(255, 0, 0, 0.5);
  pointer-events: none;
  cursor: none;
  transform: translate(-50%, -50%);
}
.follower::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
