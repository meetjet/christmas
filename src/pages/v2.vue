<script>
import Upload from 'upload-js'
// const { Upload } = pkg
import LoadImage from '~/components/LoadImage.vue'

export default {
  name: 'ChristmasPage',
  components: {
    LoadImage,
  },
  layouts: 'christmas',
  data() {
    return {
      CANVAS: null,
      CTX: null,
      CANVAS_WIDTH: 320,
      CANVAS_HEIGHT: 480,
      frameLeftSide: 281,
      frameTopSide: 104,
      frameTopCenter: 666,
      frameWidth: 208,
      frameHeight: 322,
      watermarkMaxWidth: 208,
      watermarkMaxHeight: 322,
      zoomStep: 10,
      watermark: null,
      watermarkLoaded: false,
      positionY: null,
      positionX: null,
      frame: null,
      frameLoaded: false,
      frameLoading: false,
      activeFrame: 0,
      step: 1,
    }
  },
  methods: {},
}
</script>

<template>
  <img
    v-if="step === 1"
    src="/logo-svg.svg"
    alt="лого лика ставок"
    width="250"
    height="260"
    style="margin-bottom: 95px;"
  >

  <LoadImage v-if="step === 1" @loaded="watermark = $event; step = 2" />
  <SelectFrame v-if="step === 2" @selected="activeFrame = $event; step = 3" />
  <Canvas v-if="step === 3" :watermark="watermark" :active-frame="activeFrame" />
</template>

<style>
.btn-group {
  display: flex;
}

.btn-group__btn {
  width: 415px;
  height: 310px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: transparent;
  border: solid 7px #00AA50;
  padding: 50px;
  cursor: pointer;
}

.btn-group__btn--hover:hover {
  background-color: rgba(249, 250, 251, 0.1);
  transition: background-color 0.3s ease-in-out;
}

.btn-group__text {
  display: flex;
  color: #00AA50;
  font-size: 32px;
  margin-bottom: 36px;
}
</style>
