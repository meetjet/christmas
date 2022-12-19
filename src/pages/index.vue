<script>
import LoadImage from '~/components/LoadImage.vue'
import UploadBlob from '~/components/UploadBlob.vue'

export default {
  name: 'ChristmasPage',
  components: {
    UploadBlob,
    LoadImage,
  },
  layouts: 'christmas',
  data() {
    return {
      watermark: null,
      blob: null,
      activeFrame: 0,
      step: 1,
    }
  },
  methods: {},
}
</script>

<template>
  <template v-if="step === 1 || step === 4">
    <img src="/logo-svg.svg" alt="лого лика ставок" width="250" height="260" style="margin-bottom: 95px;">
  </template>

  <LoadImage v-if="step === 1" @loaded="watermark = $event; step = 2" />
  <SelectFrame v-if="step === 2" @selected="activeFrame = $event; step = 3" />
  <Canvas v-if="step === 3" :watermark="watermark" :active-frame="activeFrame" @rendered="blob = $event; step = 4" />
  <UploadBlob v-if="step === 4" :blob="blob" />
</template>
