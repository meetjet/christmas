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
      <img class="max-w-[180px]" src="/logo.svg" alt="Лого Лига Ставок">
    </a>
  </template>

  <template v-if="step === 1">
    <p class="text-white">
      Простые шаги для поднятия новогоднего настроения:
    </p>
    <ul class="list-decimal text-white">
      <li>Загрузи готовое фото/ сделай новое</li>
      <li>Примерь новогодние рамки</li>
      <li>Сохрани получившееся фото и поделись им в соц. сетях</li>
      <li>Загляни на эту страничку 24 декабря, чтобы увидеть сюрприз</li>
    </ul>
    <div class="px-4"></div>
  </template>

  <template v-if="step === 2 || step === 3">
    <a
      href="https://ligastavoknewyear.ru/"
      class="border-2 border-[#00AA50] p-2 text-[#00AA50] select-none bg-gray-50 bg-opacity-10 hover:bg-opacity-20 transition-color duration-300 mb-4"
    >Вернуться в начало</a>
  </template>

  <LoadImage v-if="step === 1" @loaded="watermark = $event; step = 2" @take="step = 'take'"/>
  <TakePhoto v-if="step === 'take'" @loaded="watermark = $event; step = 2"/>
  <SelectFrame v-if="step === 2" @selected="activeFrame = $event; step = 3"/>
  <Canvas v-if="step === 3" :watermark="watermark" :active-frame="activeFrame" @rendered="blob = $event; step = 4"/>
  <UploadBlob v-if="step === 4" :blob="blob"/>
</template>
