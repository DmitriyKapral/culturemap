<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !flag }">
      <button class="object__close" @click="closeAside"></button>
      <button @click="locationButtonPressed">Нажми на меня</button>
      <div class="object__title"></div>
      <h2>{{ name }}</h2>
      <div v-html="description"></div>
      <div class="object__img">
        <img :src="img" alt="" class="img-absolute" />
      </div>
      <div class="object__descr"></div>
    </aside>
  </div>
</template>

<script>
export default {
    emits: ["closeAside"],
    data() {
      return {
          flagClose: false,
      }  
    },
  props: {
    flag: {
      type: Boolean,
    },
    name: {
      type: String,
    },
    description: {
      type: String,
    },
    img: {
      type: String,
    },
  },
  methods: {
    closeAside() {
      this.flagClose = false;
      this.$emit('closeAside', this.flagClose);

    },
  },
  
};
</script>

<style scoped>
.img-absolute {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  -o-object-fit: cover;
  object-fit: cover;
}
.object {
  position: fixed;
  width: 40%;
  top: 0;
  left: 0%;
  bottom: 0;
  background-color: #fff;
  z-index: 1000;
  padding: 50px 30px;
  -webkit-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
  overflow-y: auto;
}
.object_closed {
  left: -40%;
}
.object__close {
  position: absolute;
  top: 30px;
  right: 30px;
  background-color: black;
  border: 0;
  width: 30px;
  height: 3px;
  -webkit-transform: rotate(-45deg);
  -ms-transform: rotate(-45deg);
  transform: rotate(-45deg);
  cursor: pointer;
}
.object__close::after {
  content: "";
  display: block;
  width: 30px;
  height: 3px;
  background-color: #999;
  -webkit-transform: rotate(90deg);
  -ms-transform: rotate(90deg);
  transform: rotate(90deg);
  position: absolute;
  top: 0;
  left: 0;
}
.object__title {
  font-size: 32px;
  font-weight: bold;
  margin-bottom: 15px;
}
.object__img {
  position: relative;
  padding-bottom: 65%;
  margin-bottom: 25px;
}
</style>