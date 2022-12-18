<script>
export default {
  name: 'LoadImageComponent',
  emits: ['loaded'],
  methods: {},
}
</script>

<template>
  <div class="btn-group space-x-6">
    <template v-if="false">
      <button type="button" class="btn-group__btn btn-group__btn--hover">
        <span class="btn-group__text">Сделать фото</span>
        <img src="/camera.svg" alt="camera svg icon" width="185" height="135">
      </button>
    </template>

    <label for="imageLoader" class="btn-group__btn btn-group__btn--hover">
      <input
        id="imageLoader"
        ref="imageLoader"
        type="file"
        name="imageLoader"
        class="hidden"
        @change="$emit('loaded', $event.target.files[0])"
      >
      <span class="btn-group__text">Загрузить фото</span>
      <img src="/images.svg" alt="camera svg icon" width="151" height="135">
    </label>
  </div>
</template>
