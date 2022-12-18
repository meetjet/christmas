<script>
export default {
  name: 'SelectFrameComponent',
  emits: ['selected'],
  data() {
    return {
      activeFrame: null,
      frames: [
        'frame_1',
        'frame_2',
        'frame_3',
        'frame_4',
        'frame_5',
      ],
    }
  },
}
</script>

<template>
  <div class="w-full max-w-[1080px]">
    <ul class="flex flex-wrap justify-center -m-2">
      <li
        v-for="(frame, index) in frames"
        :key="index"
        class="group relative p-2 cursor-pointer"
        @click="$emit('selected', index)"
      >
        <img
          :src="`${frame}_200x200.jpg`"
          :alt="frame"
          width="200"
          height="200"
          class="aspect-square object-cover border-4 bg-white"
          :class="[activeFrame === index ? 'border-blue-500' : 'border-green-300']"
        >
        <img
          :src="`${frame}_240x360.jpg`"
          :alt="frame"
          width="240"
          height="400"
          class="z-10 absolute top-1/2 left-1/2 -translate-1/2 border-4 bg-white invisible group-hover:visible opacity-0 group-hover:opacity-100 transition-opacity duration-500"
          :class="[activeFrame === index ? 'border-blue-500' : 'border-green-300']"
        >
      </li>
    </ul>
  </div>
</template>
