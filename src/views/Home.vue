<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-50 to-pink-50 p-8">
    <div class="max-w-4xl mx-auto">
      <h1 class="text-4xl font-bold text-center mb-8 text-purple-800">九宫格图片分割器</h1>
      
      <div class="bg-white rounded-xl shadow-lg p-8 mb-8">
        <ImageUploader v-model="previewUrl" />
        <GridPreview :images="gridImages" />

        <div class="flex justify-center">
          <button 
            @click="processImage" 
            :disabled="!previewUrl"
            class="mr-4 px-6 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors">
            生成九宫格
          </button>
          <button 
            @click="downloadImages" 
            :disabled="!gridImages.length"
            class="px-6 py-2 bg-pink-600 text-white rounded-lg hover:bg-pink-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors">
            打包下载
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ImageUploader from '../components/ImageUploader.vue'
import GridPreview from '../components/GridPreview.vue'
import JSZip from 'jszip'
import { saveAs } from 'file-saver'

export default {
  name: 'Home',
  components: {
    ImageUploader,
    GridPreview
  },
  data() {
    return {
      previewUrl: '',
      gridImages: []
    }
  },
  methods: {
    processImage() {
      const img = new Image()
      img.onload = () => {
        // 计算正方形尺寸（取最大边长）
        const size = Math.max(img.width, img.height)
        
        // 创建临时画布来制作正方形图片
        const tempCanvas = document.createElement('canvas')
        const tempCtx = tempCanvas.getContext('2d')
        tempCanvas.width = size
        tempCanvas.height = size
        
        // 计算居中位置
        const offsetX = (size - img.width) / 2
        const offsetY = (size - img.height) / 2
        
        // 在临时画布上绘制居中的图片
        tempCtx.fillStyle = '#FFFFFF'
        tempCtx.fillRect(0, 0, size, size)
        tempCtx.drawImage(img, offsetX, offsetY)
        
        // 分割成九宫格
        const partSize = size / 3
        this.gridImages = []
        
        // 创建输出画布
        const outputCanvas = document.createElement('canvas')
        const outputCtx = outputCanvas.getContext('2d')
        outputCanvas.width = partSize
        outputCanvas.height = partSize
        
        for (let row = 0; row < 3; row++) {
          for (let col = 0; col < 3; col++) {
            // 清除上一次的内容
            outputCtx.clearRect(0, 0, partSize, partSize)
            
            // 绘制当前部分
            outputCtx.drawImage(
              tempCanvas,
              col * partSize,
              row * partSize,
              partSize,
              partSize,
              0,
              0,
              partSize,
              partSize
            )
            
            // 保存为base64
            this.gridImages.push(outputCanvas.toDataURL('image/png'))
          }
        }
      }
      img.src = this.previewUrl
    },
    async downloadImages() {
      const zip = new JSZip()
      
      this.gridImages.forEach((dataUrl, index) => {
        const base64Data = dataUrl.replace(/^data:image\/(png|jpg);base64,/, '')
        zip.file(`grid-${index + 1}.png`, base64Data, { base64: true })
      })
      
      const content = await zip.generateAsync({ type: 'blob' })
      saveAs(content, 'grid-images.zip')
    }
  }
}
</script>