<template>
  <div v-for="item in data" v-bind:key="item._id">
    <l-marker
      :lat-lng="[
        item.data.general.address.mapPosition.coordinates[1],
        item.data.general.address.mapPosition.coordinates[0],
      ]"
      :icon="icon"
      @click="getInfo(item)"
      ></l-marker>
  </div>
</template>

<script>
import { LMarker } from "@vue-leaflet/vue-leaflet";

export default {
  emits: ["getInfo"],
  components: {
    LMarker,
  },

  props: {
    data: {
      type: Array,
      required: true,
    },
    icon: {
      type: Object
    }
  },
  data() {
    return {
      flag: false,
      info: {
        name: '',
        description: '',
        img: '',
        contacts: [],
        workingSchedule: [],
      }
    }

  },
  methods: {
    getInfo(data) {
      this.flag = true;
      this.info.name = data.data.general.name;
      this.info.description = data.data.general.description;
      this.info.img = data.data.general.image.url;
      this.info.contacts = data.data.general.contacts;
      this.info.workingSchedule = data.data.general.workingSchedule;
      this.$emit('getInfo', this.info, this.flag);
    }
  }
  
};
</script>

<style scoped>
</style>