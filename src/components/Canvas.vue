<script>
import SaveImageButton from '~/components/SaveImageButton.vue';
import ZoomButton from '~/components/ZoomButton.vue'
import base64toBlob from '../util/base64toBlob'

export default {
  name: 'CanvasComponent',
  components: {
    SaveImageButton,
    ZoomButton,
  },
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
        {
          src: 'frame_1.png',
          top: 0,
          left: 0,
          width: 480,
          height: 720
        },
        {
          src: 'frame_2.png',
          top: 0,
          left: 0,
          width: 480,
          height: 720
        },
        {
          src: 'frame_3.png',
          top: 0,
          left: 0,
          width: 480,
          height: 720
        },
        {
          src: 'frame_4.png',
          top: 200,
          left: 135,
          width: 210,
          height: 320
        },
        {
          src: 'frame_5.png',
          top: 190,
          left: 120,
          width: 250,
          height: 350
        },
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
          let newSize = this.scaleDown(img.width, img.height, this.frame.width, this.frame.height)

          if (!newSize) {
            newSize = this.scaleUp(img.width, img.height, this.frame.width, this.frame.height)
          }

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

      this.CTX.fillStyle = 'white'
      this.CTX.fillRect(0, 0, this.CANVAS_WIDTH, this.CANVAS_HEIGHT)

      if (this.blob) {
        const x = this.blob.getAttribute('x')
        const y = this.blob.getAttribute('y')

        this.CTX.drawImage(this.blob, x, y, this.blob.width, this.blob.height)
      }

      if (this.frame) {
        this.CTX.drawImage(this.frame, 0, 0, this.CANVAS_WIDTH, this.CANVAS_HEIGHT)
      }
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
      if (!this.positionY || !this.positionX || isNaN(this.wmy) || isNaN(this.wmx)) {
        return
      }

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
    submitHandler() {
      const file = base64toBlob(
        this.CANVAS.toDataURL('image/jpeg'),
        'image/jpeg',
      )

      this.$emit('rendered', file)
    },
  },
}
</script>

<template>
  <div class="flex flex-col justify-center items-center space-y-4">
    <div class="flex justify-center items-center">
      <ZoomButton zoom="out" class="hidden sm:block mr-4" @on-click="zoomHandler"/>

      <canvas
        id="canvas"
        ref="canvas"
        class="bg-white"
        :style="`width: ${CANVAS_WIDTH}px; height: ${CANVAS_HEIGHT}px;`"
        @mousedown="mouseDownHandler"
        @mouseup="mouseUpHandler"
        @mousemove="mouseMoveHandler"
      />

      <ZoomButton zoom="in" class="hidden sm:block ml-4" @on-click="zoomHandler"/>
    </div>

    <div class="flex justify-center space-x-4 sm:hidden">
      <ZoomButton zoom="out" @on-click="zoomHandler"/>
      <ZoomButton zoom="in" @on-click="zoomHandler"/>
    </div>

    <SaveImageButton @click="submitHandler"/>
  </div>
</template>
