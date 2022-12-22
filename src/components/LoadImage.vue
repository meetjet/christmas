<script>
export default {
  name: 'LoadImageComponent',
  emits: ['loaded', 'take'],
}
</script>

<template>
  <div class="lg:flex space-y-6 lg:space-y-0 lg:space-x-6">
    <button type="button" class="btn-group__btn btn-group__btn--hover" @click="$emit('take')">
      <span class="btn-group__text">Сделать фото</span>
      <img src="/camera.svg" alt="camera svg icon" width="185" height="135">
    </button>

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

<style scoped>
.btn-group__btn {
  width: 340px;
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

@media screen and (min-width: 768px) {
  .btn-group__btn {
    width: 415px;
    height: 310px;
  }
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
