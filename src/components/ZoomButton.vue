<script>
export default {
  name: 'ZoomButton',
  inheritAttrs: true,
  props: {
    zoom: {
      type: String,
      default: 'in',
      validate: value => ['in', 'out'].includes(value),
    },
  },
  emits: ['onClick'],
  data() {
    return {
      options: {
        in: 1,
        out: -1,
      },
    }
  },
  methods: {
    clickHandler() {
      this.$emit('onClick', this.options[this.zoom])
    },
  },
}
</script>

<template>
  <button
    type="button"
    class="border-2 border-[#00AA50] py-2 px-2 cursor-pointer rounded-lg bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
    @click="$emit('onClick', options[zoom])"
  >
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="20"
      height="20"
      viewBox="0 0 512 512"
    >
      <template v-if="zoom === 'in'">
        <path
          d="M496 192h-176v-176c0-8.836-7.164-16-16-16h-96c-8.836 0-16 7.164-16 16v176h-176c-8.836 0-16 7.164-16 16v96c0 8.836 7.164 16 16 16h176v176c0 8.836 7.164 16 16 16h96c8.836 0 16-7.164 16-16v-176h176c8.836 0 16-7.164 16-16v-96c0-8.836-7.164-16-16-16z"
          fill="#00AA50"
        />
      </template>
      <template v-if="zoom === 'out'">
        <path
          d="M0 208v96c0 8.836 7.164 16 16 16h480c8.836 0 16-7.164 16-16v-96c0-8.836-7.164-16-16-16h-480c-8.836 0-16 7.164-16 16z"
          fill="#00AA50"
        />
      </template>
    </svg>
  </button>
</template>
