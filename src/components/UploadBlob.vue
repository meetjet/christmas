<script>
import pkg from 'upload-js'
const { Upload } = pkg

export default {
  name: 'UploadBlobComponent',
  props: {
    blob: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      progress: 0,
      loaded: false,
    }
  },
  async mounted() {
    const upload = Upload({
      apiKey: 'free',
    })

    const { fileUrl } = await upload.uploadFile(this.blob, {
      onBegin: () => {},
      onProgress: ({ progress }) => this.progress = progress,
    })

    this.createLink(fileUrl)
  },
  methods: {
    createLink(url) {
      fetch(url)
        .then(resp => resp.blob())
        .then((blob) => {
          const url = window.URL.createObjectURL(blob)

          this.$refs.link.setAttribute('href', url)
          this.$refs.link.click()

          this.loaded = true
        })
        .catch(() => {
          this.$refs.elem.innerHTML = 'Ошибка! Попробуйте снова.'
        })
    },
  },
}
</script>

<template>
  <div class="px-2 text-white" :class="[loaded ? 'visible' : 'invisible']">
    Скачивание изображения начнется автоматически. Если этого не произошло, нажмите на <a ref="link" href="" class="text-[#00AA50]" download>скачать</a>
  </div>
  <div v-if="!loaded" ref="elem" class="progress">
    <div class="progress-bar" :style="`width: ${progress}%`" />
    <div class="progress-text">{{progress}}%</div>
  </div>
</template>

<style scoped>
.progress {
  position: relative;
  overflow-x: hidden;
  height: 40px;
  width: 400px;
  background-color: #959595;
}

.progress-bar {
  width: 100%;
  height: 100%;
  background: #00AA50;
}

.progress-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 18px;
  padding: 3px 0;
  font-weight: 500;
}
</style>
