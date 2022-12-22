<script>
// import { Upload } from 'upload-js'
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
      // apiKey: 'public_W142hZG8ZQQAfqXjwEEYtBbxeqQb',
      apiKey: 'public_kW15az87NcfuAL44axpzXaNUazuc',
    })

    const { fileUrl } = await upload.uploadFile(this.blob, {
      onBegin: () => {},
      onProgress: ({ progress }) => this.progress = progress,
    })

    this.loaded = true
    this.createLink(fileUrl)
  },
  methods: {
    createLink(url) {
      let filename = url.split('/')

      filename = filename[filename.length - 1]

      fetch(url)
        .then(resp => resp.blob())
        .then((blob) => {
          const url = window.URL.createObjectURL(blob)

          this.$refs.link.setAttribute('download', filename)
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
  <div v-if="loaded" class="px-2 font-bold h-[40px]">
    <div class="text-center">Изображение успешно сохранено.</div>
    <div class="text-center">Скачивание начнется автоматически, но если этого не произошло, нажмите <a ref="link" href="" target="_blank" class="text-[#00AA50]"> скачать</a></div>
  </div>
  <div v-else ref="elem" class="progress">
    <div class="progress-bar" :style="`width: ${progress}%`" />
    <div class="progress-text">{{ progress }}%</div>
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
