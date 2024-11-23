<template>
  <div v-if="images.length" class="mb-8">
    <h2 class="text-2xl font-semibold mb-4 text-gray-800">九宫格预览</h2>
    <div class="grid grid-cols-3 gap-2">
      <div 
        v-for="(img, index) in images" 
        :key="index"
        class="aspect-square overflow-hidden rounded-lg shadow-sm">
        <img 
          :src="img" 
          class="w-full h-full object-cover"
          :alt="'Grid part ' + (index + 1)">
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GridPreview',
  props: {
    images: {
      type: Array,
      required: true
    }
  }
}
</script>