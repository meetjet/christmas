<script>
export default {
  name: 'ChristmasPage',
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
    <a href="https://ligastavoknewyear.ru/" target="_blank" class="flex mb-[50px]">
      <img src="/logo.svg" alt="Лого Лига Ставок" width="250" height="260">
    </a>
  </template>

  <template v-if="step === 2 || step === 3 || step === 4">
    <a
      href="https://ligastavoknewyear.ru/"
      class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300"
    >Вернуться на главную</a>
  </template>

  <LoadImage v-if="step === 1" @loaded="watermark = $event; step = 2" @take="step = 'take'"/>
  <TakePhoto v-if="step === 'take'" @loaded="watermark = $event; step = 2"/>
  <SelectFrame v-if="step === 2" @selected="activeFrame = $event; step = 3"/>
  <Canvas v-if="step === 3" :watermark="watermark" :active-frame="activeFrame" @rendered="blob = $event; step = 4"/>
  <UploadBlob v-if="step === 4" :blob="blob"/>
</template>
