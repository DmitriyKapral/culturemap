<template>
  <div id="map">
    <l-map :zoom="zoom" :center="[centerLat, centerLon]">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <get-markers
        v-if="selectedCategory.includes('libraries')"
        :data="dataLibrary"
        :icon="iconLibrary"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('cinema')"
        :data="dataCinema"
        :icon="iconCinema"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('circuses')"
        :data="dataCircuses"
        :icon="iconCircuses"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('concert_halls')"
        :data="dataConcert"
        :icon="iconConcert"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('museums')"
        :data="dataMuseums"
        :icon="iconMuseums"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('parks')"
        :data="dataParks"
        :icon="iconParks"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('theaters')"
        :data="dataTheaters"
        :icon="iconTheaters"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('culture_palaces_clubs')"
        :data="dataСulturePalacesClubs"
        :icon="iconСulturePalacesClubs"
        @getInfo="getData"
      />
    </l-map>
    <info-panel
      :flag="flag"
      :name="name"
      :description="description"
      :img="img"
      :contacts="contacts"
      :workingSchedule="workingSchedule"
      @closeAside="closeAside"
    />
    <form class="filter">
      <div>
        <fieldset>
          <legend>Фильтры объектов</legend>
          <select
            class="form-select form-select-lg mb-3"
            aria-label=".form-select-lg example"
            v-model="selectRadius"
          >
            <option value="500">500 метров</option>
            <option value="100">1000 метров</option>
            <option value="1500">1500 метров</option>
            <option value="3000">3000 метров</option>
            <option value="100000">Весь город</option>
          </select>
          <div class="v-options"></div>
          <br />
          <select
            class="form-select"
            multiple
            aria-label="multiple select example"
            v-model="selectCategory"
          >
            <option value="libraries">Библиотеки</option>
            <option value="culture_palaces_clubs">ДК и клубы</option>
            <option value="cinema">Кинотеатры</option>
            <option value="circuses">Цирки</option>
            <option value="concert_halls">Концертные залы</option>
            <option value="museums">Музеи</option>
            <option value="parks">Парки</option>
            <option value="theaters">Театры</option>
          </select>
          <br />
          <button @click="ClickData" type="button" class="btn btn-outline-dark">Применить</button>
        </fieldset>
      </div>
    </form>
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

export default {
  name: "App",
  components: { LMap, LTileLayer, GetMarkers, InfoPanel },
  data() {
    return {
      contacts: [],
      workingSchedule: [],
      selectRadius: 100000,
      selectCategory: [
        "libraries",
        "culture_palaces_clubs",
        "cinema",
        "circuses",
        "concert_halls",
        "museums",
        "parks",
        "theaters",
      ],
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
      dataLibrary: [],
      dataCinema: [],
      dataCircuses: [],
      dataConcert: [],
      dataMuseums: [],
      dataParks: [],
      dataTheaters: [],
      dataСulturePalacesClubs: [],
      dadadadadad: [],
      centerLat: 48.778444,
      centerLon: 44.777472,
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 9,
      center: [55.5807, 37.3656],
      markerLatLng: [51.504, -0.159],
      flag: false,

      name: "",
      description: "",
      img: "",

      iconLibrary: icon({
        iconUrl:
          '/icons/book.png',
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCinema: icon({
        iconUrl:
          "/icons/cinema.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCircuses: icon({
        iconUrl:
          "/icons/circuses.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconConcert: icon({
        iconUrl:
          "/icons/concert.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconMuseums: icon({
        iconUrl:
          "/icons/museum.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconParks: icon({
        iconUrl:
          "/icons/parks.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconTheaters: icon({
        iconUrl:
          "/icons/theaters.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconСulturePalacesClubs: icon({
        iconUrl:
          "/icons/culturepalacesclubs.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
    };
  },
  methods: {
    //Очень плохо реальзованный фильтр, но рабочий)
    async getRadiusData(category, lat, long, radius) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/Волгоград/",
          {
            lat: lat,
            long: long,
            radius: radius,
          }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async ClickData() {
      this.selectedCategory = this.selectCategory;
      this.dataLibrary = await this.getRadiusData("libraries", this.centerLat, this.centerLon, this.selectRadius);
      this.dataCinema = await this.getRadiusData("cinema", this.centerLat, this.centerLon, this.selectRadius);
      this.dataCircuses = await this.getRadiusData("circuses", this.centerLat, this.centerLon, this.selectRadius);
      this.dataConcert = await this.getRadiusData("concert_halls", this.centerLat, this.centerLon, this.selectRadius);
      this.dataMuseums = await this.getRadiusData("museums", this.centerLat, this.centerLon, this.selectRadius);
      this.dataParks = await this.getRadiusData("parks", this.centerLat, this.centerLon, this.selectRadius);
      this.dataTheaters = await this.getRadiusData("theaters", this.centerLat, this.centerLon, this.selectRadius);
      this.dataСulturePalacesClubs = await this.getRadiusData(
        "culture_palaces_clubs", this.centerLat, this.centerLon, this.selectRadius
      );
      
    },
 
    async fetchData(category) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/Волгоград/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    getData(data, flag) {
      try {
        this.flag = flag;
        this.name = data.name;
        this.description = data.description;
        this.img = data.img;
        this.contacts = data.contacts;
        this.workingSchedule = data.workingSchedule;
      } catch {
        alert("ошибка");
      }
    },
    
    closeAside(flag) {
      this.flag = flag;
    },
  },
  async mounted() {
    this.dataLibrary = await this.fetchData("libraries");
    this.dataCinema = await this.fetchData("cinema");
    this.dataCircuses = await this.fetchData("circuses");
    this.dataConcert = await this.fetchData("concert_halls");
    this.dataMuseums = await this.fetchData("museums");
    this.dataParks = await this.fetchData("parks");
    this.dataTheaters = await this.fetchData("theaters");
    this.dataСulturePalacesClubs = await this.fetchData(
      "culture_palaces_clubs"
    );
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
.filter {
  position: fixed;
  top: 100vw;
  background-color: #fff;
  z-index: 900;
  top: 0px;
  left: 60px;
  margin: 10px;
}

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>
