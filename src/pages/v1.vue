<script>
import pkg from 'upload-js'
const { Upload } = pkg

export default {
  name: 'ChristmasPage',
  layouts: 'christmas',
  data() {
    return {
      CANVAS: null,
      CTX: null,
      CANVAS_WIDTH: 768,
      CANVAS_HEIGHT: 512,
      frameLeftSide: 281,
      frameTopSide: 104,
      frameTopCenter: 666,
      frameWidth: 208,
      frameHeight: 322,
      watermarkMaxWidth: 208,
      watermarkMaxHeight: 322,
      zoomStep: 10,
      baseImageLoaded: false,
      baseImage: null,
      watermarkImageLoaded: false,
      watermarkImage: null,
      positionY: null,
      positionX: null,
      wmy: null,
      wmx: null,
    }
  },
  mounted() {
    this.CANVAS = this.$refs.canvas
    this.CTX = this.CANVAS.getContext('2d')
    this.loadBaseImage()
  },
  methods: {
    loadBaseImage() {
      this.baseImage = new Image()
      this.baseImage.src = 'base.png'
      this.baseImage.onload = () => {
        this.baseImageLoaded = true
        this.drawCanvas()
      }
    },
    drawCanvas() {
      this.CANVAS.width = this.CANVAS_WIDTH
      this.CANVAS.height = this.CANVAS_HEIGHT

      if (this.watermarkImageLoaded) {
        this.CTX.drawImage(
          this.watermarkImage,
          this.watermarkImage.getAttribute('x'),
          this.watermarkImage.getAttribute('y'),
          this.watermarkImage.width,
          this.watermarkImage.height,
        )
      }

      this.CTX.drawImage(this.baseImage, 0, 0, this.CANVAS_WIDTH, this.CANVAS_HEIGHT)
    },
    imageHandler(e) {
      const reader = new FileReader()

      reader.onload = (event) => {
        const img = new Image()

        img.onload = () => {
          const newSize = this.resizeImage(
            img.width,
            img.height,
            this.watermarkMaxWidth,
            this.watermarkMaxHeight,
          )

          img.width = newSize.width
          img.height = newSize.height

          img.setAttribute('x', this.frameLeftSide.toString())
          img.setAttribute('y', this.frameTopSide.toString())

          this.watermarkImage = img
          this.watermarkImageLoaded = true

          this.drawCanvas()
        }

        img.src = event.target.result
      }

      reader.readAsDataURL(e.target.files[0])
    },
    resizeImage(width, height, maxWidth = 100, maxHeight = 100) {
      let ratio = 0 // Used for aspect ratio

      // Check if the current width is larger than the max
      if (width > maxWidth) {
        ratio = maxWidth / width // get ratio for scaling image

        return {
          width: maxWidth,
          height: (height * ratio),
        }
      }

      // Check if current height is larger than max
      if (height > maxHeight) {
        ratio = maxHeight / height // get ratio for scaling image

        return {
          width: width * ratio,
          height: maxHeight,
        }
      }
    },
    mouseDownHandler(e) {
      if (!this.watermarkImage)
        return

      this.positionY = e.offsetY
      this.positionX = e.offsetX

      const clickableFrameWidth = e.offsetX >= this.frameLeftSide && e.offsetX <= (this.frameLeftSide + this.frameWidth)
      const clickableFrameHeight = e.offsetY >= this.frameTopSide && e.offsetY <= (this.frameTopSide + this.frameHeight)

      if (!clickableFrameWidth || !clickableFrameHeight)
        return

      this.wmy = parseInt(this.watermarkImage.getAttribute('y'))
      this.wmx = parseInt(this.watermarkImage.getAttribute('x'))
    },
    mouseUpHandler() {
      this.positionY = null
      this.positionX = null
      this.wmy = null
      this.wmx = null
    },
    mouseMoveHandler(e) {
      if (!this.positionY || !this.positionX || !this.wmy || !this.wmx)
        return

      const moveY = e.offsetY - this.positionY
      const moveX = e.offsetX - this.positionX

      this.watermarkImage.setAttribute('y', this.wmy + moveY)
      this.watermarkImage.setAttribute('x', this.wmx + moveX)

      this.drawCanvas()
    },
    zoomHandler(direction) {
      const img = this.watermarkImage
      const y = parseInt(img.getAttribute('y'))
      const x = parseInt(img.getAttribute('x'))

      if (direction < 0) {
        img.setAttribute('y', y + (this.zoomStep / 2))
        img.setAttribute('x', x + (this.zoomStep / 2))
      }
      else {
        img.setAttribute('y', y - (this.zoomStep / 2))
        img.setAttribute('x', x - (this.zoomStep / 2))
      }

      img.width = img.width + direction
      img.height = img.height + direction

      this.watermarkImage = img
      this.drawCanvas()
    },
    dataURLtoBlob(dataURL) {
      const array = []
      const binary = atob(dataURL.split(',')[1])
      const len = binary.length
      let i = 0

      while (i < len) {
        array.push(binary.charCodeAt(i))
        i++
      }

      return new Blob([new Uint8Array(array)], {
        type: 'image/png',
      })
    },
    async uploadImageHandler() {
      const upload = Upload({ apiKey: 'free' })
      const file = this.dataURLtoBlob(canvas.toDataURL('image/jpeg'))
      const { fileUrl, filePath } = await upload.uploadFile(file, {
        onBegin: this.onBegin,
        onProgress: this.onProgress,
      })

      console.log(`File uploaded to: ${fileUrl}`)
    },
    onBegin() {
      console.log('onBegin')
    },
    onProgress({ progress }) {
      console.log('onProgress', progress)
    },
  },
}
</script>

<template>
  <div>
    <input
      id="imageLoader"
      ref="imageLoader"
      type="file"
      name="imageLoader"
      class="hidden"
      @change="imageHandler"
    >

    <div class="flex justify-between items-center space-x-2 mb-2">
      <label for="imageLoader" class="text-sm border rounded py-1 px-2 cursor-pointer">
        {{ $t('button.add_image') }}
      </label>
      <template v-if="watermarkImageLoaded">
        <button
          type="button"
          class="text-sm border rounded py-1 px-2 cursor-pointer"
          @click="uploadImageHandler"
        >
          {{ $t('button.upload_image') }}
        </button>
      </template>
    </div>

    <canvas
      id="canvas"
      ref="canvas"
      class="w-[768px] h-[512px]"
      @mousedown="mouseDownHandler"
      @mouseup="mouseUpHandler"
      @mousemove="mouseMoveHandler"
    />

    <div class="flex justify-center space-x-2 mt-2 h-[30px]">
      <template v-if="watermarkImageLoaded">
        <button
          type="button"
          class="text-sm border rounded py-1 px-2 cursor-pointer"
          @click="zoomHandler(+zoomStep)"
        >
          {{ $t('button.zoom_in') }}
        </button>
        <button
          type="button"
          class="text-sm border rounded py-1 px-2 cursor-pointer"
          @click="zoomHandler(-zoomStep)"
        >
          {{ $t('button.zoom_out') }}
        </button>
      </template>
    </div>
  </div>
</template>

<style scoped>
#canvas {
  background-image: url("data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/2wBDAAoHBwgHBgoICAgLCgoLDhgQDg0NDh0VFhEYIx8lJCIfIiEmKzcvJik0KSEiMEExNDk7Pj4+JS5ESUM8SDc9Pjv/2wBDAQoLCw4NDhwQEBw7KCIoOzs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozv/wAARCAAKAAoDASIAAhEBAxEB/8QAFwAAAwEAAAAAAAAAAAAAAAAAAAMFB//EACUQAAEDAQYHAAAAAAAAAAAAAAEAAgMFERIVITE0QVJxcpGx0f/EABQBAQAAAAAAAAAAAAAAAAAAAAD/xAAUEQEAAAAAAAAAAAAAAAAAAAAA/9oADAMBAAIRAxEAPwDU4onU1xmmIc1wugMzNuvHom4rBySeB9RVdq3vHoqSg//Z");
}
</style>
