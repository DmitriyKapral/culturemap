<template>
  <div v-for="item in data" v-bind:key="item._id">
    <l-marker
      :lat-lng="[
        item.data.general.address.mapPosition.coordinates[1],
        item.data.general.address.mapPosition.coordinates[0],
      ]"
      :icon="icon"
      @click="getInfo(item)"
    >
    <l-tooltip>{{ item.data.general.name }}</l-tooltip>
    </l-marker>
  </div>
</template>

<script>
import { LMarker, LTooltip } from "@vue-leaflet/vue-leaflet";

export default {
  emits: ["getInfo"],
  components: {
    LMarker,
    LTooltip,
  },

  props: {
    data: {
      type: Array,
      required: true,
    },
    icon: {
      type: Object,
    },
    events: {
      type: Array,
    },
  },
  data() {
    return {
      flag: false,
      info: {
        name: "",
        description: "",
        address: "",
        img: "",
        contacts: [],
        workingSchedule: [],
        filterEvents: [],
      },
    };
  },
  methods: {
    getInfo(item) {
      this.flag = true;
      this.info.name = item.data.general.name;
      this.info.description = item.data.general.description;
      this.info.address = item.data.general.address.fullAddress;
      this.info.img = item.data.general.image.url;
      this.info.contacts = item.data.general.contacts;
      this.info.workingSchedule = item.data.general.workingSchedule;
      this.info.filterEvents = this.events.filter(
        (event) => event.data.general.places[0].id == item.data.general.id
      );
      this.$emit("getInfo", this.info, this.flag);
    },
  },
};
</script>

<style scoped>
</style>