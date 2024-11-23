<template>
  <div class="mb-6">
    <label class="block w-full cursor-pointer">
      <div 
        class="border-2 border-dashed border-purple-300 rounded-lg p-8 text-center hover:border-purple-500 transition-colors"
        :class="{ 'border-purple-500': isDragging }">
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
      </div>
      <input 
        type="file" 
        class="hidden" 
        accept="image/*"
        @change="handleFileSelect"
        @dragenter="isDragging = true"
        @dragleave="isDragging = false"
        @drop="isDragging = false">
    </label>
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
      if (file && file.type.match('image.*')) {
        const reader = new FileReader()
        reader.onload = (e) => {
          this.$emit('update:modelValue', e.target.result)
        }
        reader.readAsDataURL(file)
      }
    }
  }
}
</script>