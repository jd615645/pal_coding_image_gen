<template>
  <div class="w-screen h-full min-h-screen bg-gray-200 flex flex-col items-center justify-center gap-4">
    <h2 class="text-2xl font-bold">Pal Engineer Image Generator</h2>
    <div class="border-2 border-white rounded p-4 w-full max-w-xs bg-gray-50 flex flex-col gap-4 shadow-xl">
      <div class="flex flex-col gap-2">
        <label class="text-xl font-bold">Chose your language</label>
        <select
          name="language"
          v-model="selectedLanguage"
          class="w-full p-2 border-2 border-gray-300 rounded"
        >
          <option
            v-for="lang in languages"
            :key="lang.name"
            :value="lang">
            {{ lang.name }}
          </option>
        </select>
      </div>

      <canvas ref="canvas" class="w-full block mx-auto"></canvas>
      <button class="p-2 border-2 border-gray-300 rounded hover:bg-gray-100" @click="saveImage">
        Save Image
      </button>

      <div class="flex flex-row gap-4 justify-center">
        <span
          class="underline cursor-pointer"
          @click="showAbout = true">about</span>
        <a
          class="underline cursor-pointer"
          href="#">source</a>
      </div>
    </div>

    <div v-if="showAbout" class="modal">
      <div class="modal-content">
        <p
          class="close"
          @click="showAbout = false"
        >&times;</p>
        <div class="flex flex-col gap-4">
          <p>這款小工具是出於個人興趣而製作的，它能夠幫助您創建一個迷人又可愛的帕魯（Pal）工程師頭像。這個頭像的靈感來源於我在小紅書上看到的一則貼文。原始的圖片似乎也是轉載的，且未提及原作者。如果您知道作者的身份，請隨時通過郵件 <span class="email">jd615465[at]gmail.com</span> 與我聯絡。我會非常樂意在作品中標明作者的名字。如果原作者不希望其作品以這種方式被使用，我將立即撤下相關內容。</p>
          <p>此外，歡迎各位的提交 PR 來更新更多的編程語言選項。您的參與將使這個小工具更加豐富 =D</p>

          <hr>

          <p>
            This tool is a personal passion project, allowing you to create a charming and adorable Pal Engineer avatar. The original image was inspired by a post I saw on Xiaohongshu. The picture seems to be a repost as well, without any attribution to the original artist. If you happen to know the identity of the artist, please feel free to contact me at <span class="email">jd615465[at]gmail.com</span>. I would be more than happy to credit the artist in the work. If the original artist does not wish for their work to be used in this way, I will immediately take down the content.
          </p>
          <p>
            Additionally, we warmly welcome contributors to submit pull requests (PRs) to add more programming language options. Your participation will make this fun project even more diverse and colorful!
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';
import baseImage from './pal_engineer_base.png';

const languages = ref([
  {
    name: 'JavaScript',
    code: 'const func = () => {}'
  },
  {
    name: 'TypeScript',
    code: 'const func = (): void => {}'
  },
  {
    name: 'Python',
    code: 'def func(): pass'
  },
  {
    name: 'Java',
    code: 'public void func() {}'
  },
  {
    name: 'C#',
    code: 'void Func() {}'
  },
  {
    name: 'PHP',
    code: 'function func() {}'
  },
  {
    name: 'Ruby',
    code: 'def func; end'
  },
  {
    name: 'Go',
    code: 'func func() {}'
  },
  {
    name: 'C++',
    code: 'void func() {}'
  },
  {
    name: 'Swift',
    code: 'func func() {}'
  },
  {
    name: 'Kotlin',
    code: 'fun func() {}'
  },
  {
    name: 'Rust',
    code: 'fn func() {}'
  }
]);

const showAbout = ref(false);
const selectedLanguage = ref(languages.value[0]);

const canvas = ref<HTMLCanvasElement | null>(null);
const ctx = ref<CanvasRenderingContext2D | null>(null);

watch(selectedLanguage, () => {
  drawImage();
});

const drawImage = async () => {
  const image = new Image();
  image.src = baseImage;
  image.onload = () => {
    if (!canvas.value) return;
    const context = canvas.value.getContext('2d');
    if (!context) return;

    ctx.value = context;
    canvas.value.width = image.width;
    canvas.value.height = image.height;
    ctx.value.clearRect(0, 0, canvas.value.width, canvas.value.height);
    ctx.value.drawImage(image, 0, 0);
    ctx.value.globalAlpha = 0.8;
    ctx.value.font = 'bold 32px Hack, monospace';
    ctx.value.fillStyle = '#f490aa';

    // 在旋轉和繪製文字前保存當前狀態
    ctx.value.save();

    // 移動到旋轉的中心點
    ctx.value.translate(135, 50);

    // 旋轉
    ctx.value.rotate(0.1);

    // 繪製文字（相對於旋轉後的新位置）
    ctx.value.fillText(selectedLanguage.value.code, 0, 0);

    // 恢復到旋轉前的狀態
    ctx.value.restore();
  };
};

const saveImage = () => {
  if (!canvas.value) return;
  const image = canvas.value.toDataURL('image/png').replace('image/png', 'image/octet-stream');
  const link = document.createElement('a');
  const lowerCaseStr = (selectedLanguage.value.name).toLocaleLowerCase();
  link.download = `pal_${lowerCaseStr}.png`;
  link.href = image;
  link.click();
};

drawImage();
</script>

<style>
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  font-size: 28px;
  font-weight: bold;
  text-align: right;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>