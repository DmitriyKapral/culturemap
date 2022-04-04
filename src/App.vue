<template>
  <div id="map">
    <l-map :zoom="zoom" :center="center" @update:center="centerUpdated">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <div v-for="n in 8" :key="n">
        <get-markers
          v-if="selectedCategory.includes(nameCategoryObject[n - 1])"
          :data="data[n - 1]"
          :icon="icons[n - 1]"
          :events="events[n - 1]"
          @getInfo="getData"
        />
      </div>
    </l-map>
    <info-panel :info="info" @closeAside="closeAside" />
    <filter-objects @filterObject="filterObject" />
    <filter-events @filterEvents="filterEvents" />
  </div>
</template>

<script>
//import MyDialog from "MyDialog.vue"
import axios from "axios";
import { icon } from "leaflet";
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer } from "@vue-leaflet/vue-leaflet";
import GetMarkers from "./components/GetMarkers.vue";
import InfoPanel from "./components/InfoPanel.vue";
import FilterObjects from "./components/FilterObjects.vue";
import FilterEvents from "./components/FilterEvents.vue";

export default {
  name: "App",
  components: {
    LMap,
    LTileLayer,
    GetMarkers,
    InfoPanel,
    FilterObjects,
    FilterEvents,
  },
  data() {
    return {
      selectCategoryEvents: [],
      selectedFree: 0,
      inputAge: 0,
      selectedCategory: [
        "libraries",
        "culture_palaces_clubs",
        "cinema",
        "circuses",
        "concert_halls",
        "museums",
        "parks",
        "theaters",
      ],
      city: "",
      data: [[], [], [], [], [], [], [], []],
      events: [[], [], [], [], [], [], [], []],
      filterEvent: [],
      centerLat: 48.77834045,
      centerLon: 44.77727568,
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 9,
      center: [55.5807, 37.3656],
      markerLatLng: [51.504, -0.159],
      info: {
        flag: false,
        name: "",
        description: "",
        img: "",
        contacts: [],
        workingSchedule: [],
        selectEvents: [],
      },

      filter: [],
      icons: [
        icon({
          iconUrl: "/icons/book.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/cinema.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/circuses.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/concert.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/museum.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/parks.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/theaters.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
        icon({
          iconUrl: "/icons/culturepalacesclubs.png",
          iconSize: [32, 37],
          iconAnchor: [16, 37],
        }),
      ],
      nameCategoryObject: [
        "libraries",
        "cinema",
        "circuses",
        "concert_halls",
        "museums",
        "parks",
        "theaters",
        "culture_palaces_clubs",
      ],
    };
  },
  methods: {
    async filterEvents(selectCategoryEvents, selectedFree, inputAge) {
      this.selectCategoryEvents = selectCategoryEvents;
      this.selectedFree = selectedFree;
      this.inputAge = inputAge;
      await this.loadingEvents();
      for (let i = 0; i < 8; i++) {
        this.events[i] = this.events[i]
          .filter((item) =>
            selectCategoryEvents.includes(item.data.general.category.name)
          )
          .filter((age) => age.data.general.ageRestriction == inputAge);
        if (selectedFree < 2) {
          this.events[i] = this.events[i].filter(
            (item) => item.data.general.isFree == Boolean(selectedFree)
          );
        }
      }
    },

    async fetchFilterEvents(category) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/geteventscategory/" +
            category +
            "/" +
            this.city +
            "/",
          {
            category: this.selectCategoryEvents,
            ageRestriction: this.inputAge,
            isFree: this.selectedFree,
          }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    async ClickEventsData() {
      for (let i = 0; i < 8; i++) {
        this.events[i] = await this.fetchFilterEvents(
          this.nameCategoryObject[i]
        );
      }
    },

    async getRadiusData(category) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/categoryget/" +
            category +
            "/" +
            this.city +
            "/",
          {
            lat: this.centerLat,
            long: this.centerLon,
            radius: this.selectRadius,
          }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    async filterObject(selectCategory, selectRadius) {
      this.selectedCategory = selectCategory;
      this.selectRadius = selectRadius;
      for (let i = 0; i < 8; i++) {
        this.data[i] = await this.getRadiusData(this.nameCategoryObject[i]);
      }
    },

    async fetchData(category) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/categoryget/" +
            category +
            "/" +
            this.city +
            "/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    getData(data, flag) {
      try {
        this.info.flag = flag;
        this.info.name = data.name;
        this.info.description = data.description;
        this.info.img = data.img;
        this.info.contacts = data.contacts;
        this.info.workingSchedule = data.workingSchedule;
        this.info.selectEvents = data.filterEvents;
      } catch {
        alert("ошибка");
      }
    },

    closeAside(flag) {
      this.info.flag = flag;
    },

    async getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.centerLat = position.coords.latitude;
            this.centerLon = position.coords.longitude;
            this.centerUpdated([this.centerLat, this.centerLon]);
            console.log(position.coords.latitude);
            console.log(position.coords.longitude);
          },
          (error) => {
            console.log(error.message);
          }
        );
      } else {
        alert("Разрешите доступ к данным местоположения");
      }
    },

    centerUpdated(center) {
      this.center = center;
    },

    async postCity(lat, lon) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/position/",
          {
            lat: lat,
            lon: lon,
          }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    async fetchEvents(category) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/geteventscategory/" +
            category +
            "/" +
            this.city +
            "/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async loadingEvents() {
      for (let i = 0; i < 8; i++) {
        this.events[i] = await this.fetchEvents(this.nameCategoryObject[i]);
      }
    },
  },
  async mounted() {
    //await this.getLocation();
    this.city = await this.postCity(this.centerLat, this.centerLon);
    //Загрузка объектов
    for (let i = 0; i < 8; i++) {
      this.data[i] = await this.fetchData(this.nameCategoryObject[i]);
    }
    //Загрузка событий
    await this.loadingEvents();
    alert("Events");
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#map {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
}

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>
