<script>
import Webcam from '../util/webCam'
import base64toBlob from '../util/base64toBlob'

export default {
  name: 'TakePhoto',
  emits: ['loaded'],
  data() {
    return {
      base64: null,
      webcamElement: null,
      canvasElement: null,
      snapSoundElement: null,
      webcam: null,
      webcamStarted: false,
      error: false,
      errorMessage: 'Fail to start camera, please allow permision to access camera.',
    }
  },
  beforeUnmount() {
    this.webcam.stop()
  },
  mounted() {
    this.webcamElement = document.getElementById('webcam')
    this.canvasElement = document.getElementById('canvas')
    this.snapSoundElement = document.getElementById('snapSound')

    this.webcam = new Webcam(this.webcamElement, 'user', this.canvasElement, this.snapSoundElement)

    this.startWebCam()
  },
  methods: {
    startWebCam() {
      this.webcam.start()
        .then((_result) => {
          this.webcamStarted = true
          this.base64 = null
          this.canvasElement.classList.add('hidden')
        })
        .catch(_err => this.error = true)
    },
    takeHandler() {
      this.base64 = this.webcam.snap()
      this.webcam.stop()
      this.webcamStarted = false
      this.canvasElement.classList.remove('hidden')
    },
    saveHandler() {
      this.$emit('loaded', base64toBlob(this.base64))
    },
  },
}
</script>

<template>
  <audio id="snapSound" src="/snap.mp3" preload="auto"/>

  <div class="relative" style="background-color: lightgray;">
    <video id="webcam" class="top-0 left-0" autoplay playsinline width="640" height="640"/>
    <canvas id="canvas" class="hidden absolute top-0 left-0" width="640" height="480"/>
  </div>

  <div class="h-[44px] mt-4 space-x-4">
    <button
      v-if="webcamStarted"
      class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
      @click="takeHandler"
    >
      Сделать снимок
    </button>

    <button
      v-if="base64"
      class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
      @click="saveHandler"
    >
      Сохранить
    </button>

    <button
      v-if="base64"
      class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
      @click="startWebCam"
    >
      Сделать новое
    </button>
  </div>
</template>

<style scoped>

</style>
