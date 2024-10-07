<script setup lang="ts">
import { useTemplateRef } from 'vue'

import lowImage from "../assets/HI_600_400.png"

const squareElement = useTemplateRef<HTMLDivElement>('square-element')
const imageElement = useTemplateRef<HTMLDivElement>('image')
const originImageElement = useTemplateRef<HTMLDivElement>('origin-image')

const update = (e: MouseEvent) => {
  if (!squareElement.value || !imageElement.value || !originImageElement.value) {
    return
  }

  const rect = imageElement.value.getBoundingClientRect()

  // 마우스 위치를 image 요소 내부로 제한
  const x = Math.min(Math.max(e.clientX - rect.left, 0), rect.width)
  const y = Math.min(Math.max(e.clientY - rect.top, 0), rect.height)

  // 네모의 위치 업데이트
  squareElement.value.style.left = `${x}px`
  squareElement.value.style.top = `${y}px`

  // 큰 이미지의 배경 위치 계산
  const ratioX = x / rect.width
  const ratioY = y / rect.height

  const bigImageX = ratioX * (1024 - 480) // 1024는 큰 이미지의 너비, 480은 보여줄 영역의 너비
  const bigImageY = ratioY * (720 - 362) // 720은 큰 이미지의 높이, 362는 보여줄 영역의 높이

  // 큰 이미지의 배경 위치 업데이트
  originImageElement.value.style.backgroundPosition = `-${bigImageX}px -${bigImageY}px`
}
</script>

<template>
  <main class="main">
    <div class="image" @mousemove="update" ref="image">
      <img :src="lowImage" alt="Low resolution image" />
      <div class="follower" ref="square-element"></div>
    </div>
    <div class="origin-image" ref="origin-image">
    </div>
  </main>
</template>

<style scoped>
.main {
  display: flex;
  gap: 10rem;
}
.image {
  position: relative;
  width: 600px;
  height: 400px;
  overflow: hidden;
}
.image > img {
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
.origin-image {
  width: 480px;
  height: 362px;
  overflow: hidden;
  background: url(../assets/HI_1024_720.png) no-repeat 0 0;
  background-size: 1024px 720px;
}
</style>