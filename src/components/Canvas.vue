<script>
export default {
  name: 'CanvasComponent',
  props: {
    activeFrame: {
      type: Number,
      required: true,
    },
    watermark: {
      type: Object,
      required: true,
    },
  },
  emits: ['rendered'],
  data() {
    return {
      CANVAS: null,
      CTX: null,
      CANVAS_WIDTH: 480,
      CANVAS_HEIGHT: 720,
      zoomStep: 10,
      blob: null,
      positionY: null,
      positionX: null,
      wmy: null,
      wmx: null,
      frame: null,
      frames: [
        { src: 'frame_1.png', top: 0, left: 0, width: 480, height: 720 },
        { src: 'frame_2.png', top: 0, left: 0, width: 480, height: 720 },
        { src: 'frame_3.png', top: 0, left: 0, width: 480, height: 720 },
        { src: 'frame_4.png', top: 200, left: 135, width: 210, height: 320 },
        { src: 'frame_5.png', top: 190, left: 120, width: 250, height: 350 },
      ],
    }
  },
  mounted() {
    this.CANVAS = this.$refs.canvas
    this.CTX = this.CANVAS.getContext('2d')
    this.loadFrame()
    this.loadImage(this.watermark)
  },
  methods: {
    loadFrame() {
      const activeFrame = this.frames[this.activeFrame]

      this.frame = new Image()
      this.frame.src = activeFrame.src
      this.frame.left = activeFrame.left
      this.frame.top = activeFrame.top
      this.frame.width = activeFrame.width
      this.frame.height = activeFrame.height

      this.frame.onload = this.drawCanvas
    },
    loadImage(file) {
      const reader = new FileReader()

      reader.onload = (event) => {
        const img = new Image()

        img.onload = () => {
          const newSize = this.scaleDown(img.width, img.height, this.frame.width, this.frame.height)

          img.width = newSize.width
          img.height = newSize.height

          img.setAttribute('y', this.frame.top.toString())
          img.setAttribute('x', this.frame.left.toString())

          this.blob = img
          this.drawCanvas()
        }

        img.src = event.target.result
      }

      reader.readAsDataURL(file)
    },
    drawCanvas() {
      this.CANVAS.width = this.CANVAS_WIDTH
      this.CANVAS.height = this.CANVAS_HEIGHT

      const x = this.blob.getAttribute('x')
      const y = this.blob.getAttribute('y')

      this.blob && this.CTX.drawImage(this.blob, x, y, this.blob.width, this.blob.height)
      this.frame && this.CTX.drawImage(this.frame, 0, 0, this.CANVAS_WIDTH, this.CANVAS_HEIGHT)
    },
    zoomHandler(direction) {
      const img = this.blob
      const y = parseInt(img.getAttribute('y'))
      const x = parseInt(img.getAttribute('x'))

      let newSize

      if (direction === -1) {
        img.setAttribute('y', y + (this.zoomStep / 2))
        img.setAttribute('x', x + (this.zoomStep / 2))

        newSize = this.scaleDown(
          img.width,
          img.height,
          img.width - this.zoomStep,
          img.height - this.zoomStep,
        )
      }

      if (direction === 1) {
        img.setAttribute('y', y - (this.zoomStep / 2))
        img.setAttribute('x', x - (this.zoomStep / 2))

        newSize = this.scaleUp(
          img.width,
          img.height,
          img.width + this.zoomStep,
          img.height + this.zoomStep,
        )
      }

      img.width = newSize.width
      img.height = newSize.height

      this.blob = img
      this.drawCanvas()
    },
    mouseDownHandler(e) {
      if (!this.blob)
        return

      this.positionY = e.offsetY
      this.positionX = e.offsetX
      this.wmy = parseInt(this.blob.getAttribute('y'))
      this.wmx = parseInt(this.blob.getAttribute('x'))
    },
    mouseUpHandler() {
      this.positionY = null
      this.positionX = null
      this.wmy = null
      this.wmx = null
    },
    mouseMoveHandler(e) {
      if (!this.positionY || !this.positionX || isNaN(this.wmy) || isNaN(this.wmx))
        return

      const moveY = e.offsetY - this.positionY
      const moveX = e.offsetX - this.positionX

      this.blob.setAttribute('y', this.wmy + moveY)
      this.blob.setAttribute('x', this.wmx + moveX)

      this.drawCanvas()
    },
    scaleDown(width, height, maxWidth = 100, maxHeight = 100) {
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
    scaleUp(width, height, maxWidth = 100, maxHeight = 100) {
      let ratio = 0 // Used for aspect ratio

      // Check if the current width is larger than the max
      if (width < maxWidth) {
        ratio = maxWidth / width // get ratio for scaling image

        return {
          width: maxWidth,
          height: (height * ratio),
        }
      }

      // Check if current height is larger than max
      if (height < maxHeight) {
        ratio = maxHeight / height // get ratio for scaling image

        return {
          width: width * ratio,
          height: maxHeight,
        }
      }
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
    submitHandler() {
      const file = this.dataURLtoBlob(this.CANVAS.toDataURL('image/jpeg'))

      this.$emit('rendered', file)
    },
  },
}
</script>

<template>
  <div class="flex flex-col justify-center items-center space-y-4">
    <div class="flex justify-center items-center space-x-4">
      <button
        type="button"
        class="py-2 px-2 cursor-pointer rounded-lg bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
        @click="zoomHandler(-1)"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="20"
          height="20"
          viewBox="0 0 512 512"
        >
          <path
            d="M0 208v96c0 8.836 7.164 16 16 16h480c8.836 0 16-7.164 16-16v-96c0-8.836-7.164-16-16-16h-480c-8.836 0-16 7.164-16 16z"
            fill="white"
          />
        </svg>
      </button>

      <canvas
        id="canvas"
        ref="canvas"
        class="bg-white"
        :style="`width: ${CANVAS_WIDTH}px; height: ${CANVAS_HEIGHT}px;`"
        @mousedown="mouseDownHandler"
        @mouseup="mouseUpHandler"
        @mousemove="mouseMoveHandler"
      />

      <button
        type="button"
        class="py-2 px-2 cursor-pointer rounded-lg bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
        @click="zoomHandler(1)"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="20"
          height="20"
          viewBox="0 0 512 512"
        >
          <path
            d="M496 192h-176v-176c0-8.836-7.164-16-16-16h-96c-8.836 0-16 7.164-16 16v176h-176c-8.836 0-16 7.164-16 16v96c0 8.836 7.164 16 16 16h176v176c0 8.836 7.164 16 16 16h96c8.836 0 16-7.164 16-16v-176h176c8.836 0 16-7.164 16-16v-96c0-8.836-7.164-16-16-16z"
            fill="white"
          />
        </svg>
      </button>
    </div>

    <button class="text-[#00AA50]" @click="submitHandler">
      Сохранить изображение
    </button>
  </div>
</template>
