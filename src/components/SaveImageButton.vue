<script>
export default {
  name: 'SaveImageButton',
  emits: ['click'],
}
</script>

<template>
  <button
    class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
    @click="$emit('click')"
  >
    Сохранить изображение
  </button>
</template>
