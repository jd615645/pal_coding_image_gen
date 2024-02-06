<template>
  <div
    class="w-screen min-h-screen bg-gray-200 flex flex-col items-center justify-center gap-4"
    :style="{ height: `${screenHeight}px` }"
  >
    <h2 class="text-2xl font-bold">
      Pal Engineer Image Generator
    </h2>
    <div class="border-2 border-white rounded p-4 w-full max-w-xs bg-gray-50 flex flex-col gap-4 shadow-xl">
      <div class="flex flex-col gap-2">
        <label class="text-xl font-bold">Chose your language</label>
        <select
          v-model="selectedLanguage"
          name="language"
          class="w-full p-2 border-2 border-gray-300 rounded"
        >
          <option
            v-for="lang in languages"
            :key="lang.name"
            :value="lang"
          >
            {{ lang.name }}
          </option>
        </select>
      </div>

      <canvas
        ref="canvas"
        class="w-full block mx-auto"
      />
      <button
        class="p-2 border-2 border-gray-300 rounded hover:bg-gray-100"
        @click="saveImage"
      >
        Save Image
      </button>

      <div class="flex flex-row gap-4 justify-center">
        <span
          class="underline cursor-pointer"
          @click="showAbout = true"
        >about</span>
        <a
          class="underline cursor-pointer"
          href="https://github.com/jd615645/pal_coding_image_gen"
          target="_blank"
        >source</a>
      </div>
    </div>

    <div
      v-if="showAbout"
      class="fixed inset-0 bg-black bg-opacity-40 overflow-auto z-10"
    >
      <div class="bg-white p-6 m-auto border border-gray-300 rounded w-4/5 my-4">
        <p
          class="text-right text-gray-500 text-2xl font-bold cursor-pointer"
          @click="showAbout = false"
        >
          &times;
        </p>
        <div class="flex flex-col gap-4">
          <p>這款小工具是出於個人興趣而製作的，它能夠幫助您創建一個迷人又可愛的帕魯（Pal）工程師頭像。這個頭像的靈感來源於我在小紅書上看到的一則貼文。</p>
          <p>
            原始圖片來自於小紅書的繪師
            <a
              class="font-bold underline text-blue-500"
              href="https://www.xiaohongshu.com/explore/65bb4ba90000000008023daa?app_platform=ios&app_version=8.24.2&author_share=1&share_from_user_hidden=true&type=normal&xhsshare=CopyLink&appuid=5dcc19f6000000000100862c&apptime=1707072363"
              target="_blank"
            >WEN</a>
            。歡迎大家關注
          </p>
          <p>此外，歡迎各位的提交 PR 來更新更多的程式語言選項。</p>

          <hr>

          <p>
            This tool is a personal passion project, designed to help you create a charming and adorable Pal Engineer
            avatar. The inspiration for this avatar came from a post I saw on Xiaohongshu.
          </p>
          <p>
            The original artwork is by the Xiaohongshu artist
            <a
              class="font-bold underline text-blue-500"
              href="https://www.xiaohongshu.com/explore/65bb4ba90000000008023daa?app_platform=ios&app_version=8.24.2&author_share=1&share_from_user_hidden=true&type=normal&xhsshare=CopyLink&appuid=5dcc19f6000000000100862c&apptime=1707072363"
              target="_blank"
            >WEN</a>.
            Feel free to follow and support them.
          </p>
          <p>
            In addition, we warmly welcome everyone to submit pull requests (PRs) to update more programming language
            options.
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { useWindowSize } from '@vueuse/core'
import baseImage from './pal_engineer_base.png'
import { languages } from './languages'

const { height: screenHeight } = useWindowSize()

const showAbout = ref(false)
const selectedLanguage = ref(languages[0])

const canvas = ref<HTMLCanvasElement | null>(null)
const ctx = ref<CanvasRenderingContext2D | null>(null)

const drawImage = async () => {
  const image = new Image()
  image.src = baseImage
  image.onload = () => {
    if (!canvas.value) return
    const context = canvas.value.getContext('2d')
    if (!context) return

    ctx.value = context
    canvas.value.width = image.width
    canvas.value.height = image.height
    ctx.value.clearRect(0, 0, canvas.value.width, canvas.value.height)
    ctx.value.drawImage(image, 0, 0)
    ctx.value.globalAlpha = 0.8
    ctx.value.font = 'bold 32px Hack, monospace'
    ctx.value.fillStyle = '#f490aa'

    // 在旋轉和繪製文字前保存當前狀態
    ctx.value.save()

    // 移動到旋轉的中心點
    ctx.value.translate(135, 50)

    // 旋轉
    ctx.value.rotate(0.1)

    // 繪製文字（相對於旋轉後的新位置）
    ctx.value.fillText(selectedLanguage.value.code, 0, 0)

    // 恢復到旋轉前的狀態
    ctx.value.restore()
  }
}

const saveImage = () => {
  if (!canvas.value) return
  const image = canvas.value.toDataURL('image/png').replace('image/png', 'image/octet-stream')
  const link = document.createElement('a')
  const lowerCaseStr = (selectedLanguage.value.name).toLocaleLowerCase()
  link.download = `pal_${lowerCaseStr}.png`
  link.href = image
  link.click()
}

watch(selectedLanguage, () => {
  drawImage().catch(error => console.error(error))
})

drawImage().catch(error => console.error(error))
</script>
