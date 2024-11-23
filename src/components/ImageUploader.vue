<template>
  <div class="mb-6">
    <div 
      class="border-2 border-dashed border-purple-300 rounded-lg p-8 text-center hover:border-purple-500 transition-colors"
      :class="{ 'border-purple-500': isDragging }"
      @dragenter.prevent="isDragging = true"
      @dragover.prevent="isDragging = true"
      @dragleave.prevent="isDragging = false"
      @drop.prevent="handleDrop">
      <div class="text-gray-500">
        <i class="fas fa-cloud-upload-alt text-3xl mb-4"></i>
        <p class="text-lg">点击或拖拽上传图片</p>
        <p class="text-sm">支持 JPG、PNG 格式</p>
      </div>
      <img 
        v-if="modelValue" 
        :src="modelValue" 
        class="mt-4 max-h-64 mx-auto"
        alt="Preview">
      <input 
        type="file" 
        class="hidden" 
        accept="image/*"
        @change="handleFileSelect">
    </div>
  </div>
</template>

<script>
export default {
  name: 'ImageUploader',
  props: {
    modelValue: String
  },
  emits: ['update:modelValue'],
  data() {
    return {
      isDragging: false
    }
  },
  methods: {
    handleFileSelect(event) {
      const file = event.target.files[0]
      if (file) {
        this.processFile(file)
      }
    },
    handleDrop(event) {
      this.isDragging = false
      const file = event.dataTransfer?.files[0]
      if (file) {
        this.processFile(file)
      }
    },
    processFile(file) {
      if (file.type.match('image.*')) {
        const reader = new FileReader()
        reader.onload = (e) => {
          this.$emit('update:modelValue', e.target.result)
        }
        reader.readAsDataURL(file)
      } else {
        alert('请上传图片文件（JPG、PNG 格式）')
      }
    }
  }
}
</script>