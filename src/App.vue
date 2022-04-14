<template>
  <div id="map">
    <l-map :zoom="zoom" :center="center" @update:center="centerUpdated"
    @update:zoom="zoomUpdated">
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
    <events-panel
      :events="events"
      @moveToMarker="moveToMarker"
      v-model:show="panelVisible"
    />
    <div class="b1">
      <button type="button"  @click="showFilter">Нажать</button>
   </div>
    <my-filter v-model:show="filterObjectVisible">
      <filter-objects @filterObject="filterObject" />
    </my-filter>
    
    <div class="b2">
      <button type="button"  @click="showAnalysis">Нажать</button>
   </div>
    <filter-events @filterEvents="filterEvents" 
    @allEvents="allEvents"/>

    <analysis-panel :city="city"
    v-model:show="analysisPanelVisible"/>
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
import MyFilter from './components/UI/MyFilter.vue';
import EventsPanel from './components/EventsPanel.vue';
import AnalysisPanel from './components/AnalysisPanel.vue'

export default {
  name: "App",
  components: {
    LMap,
    LTileLayer,
    GetMarkers,
    InfoPanel,
    FilterObjects,
    FilterEvents,
    MyFilter,
    EventsPanel,
    AnalysisPanel,
  },
  data() {
    return {
      analysisPanelVisible: false,
      panelVisible: false,
      filterObjectVisible: false,
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
      parsingEvents: [],
      events: [[], [], [], [], [], [], [], []],
      filterEvent: [],
      centerLat: 48.77834045,
      centerLon: 44.77727568,
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 11,
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
      categoryObject: ['Библиотеки', 'Кинотеатры', 'Цирки', 'Концертные залы', 'Музеи и галереи', 'Парки', 'Театры', 'Дворцы культуры и клубы'],
    };
  },
  methods: {
    filterEvents(selectCategoryEvents, selectedFree, inputAge) {
      this.loadingEvents();
      for (let i = 0; i < 8; i++) {
        this.events[i] = this.events[i]
          .filter((item) =>
            selectCategoryEvents.includes(item.data.general.category.name)
          )
          .filter((age) => age.data.general.ageRestriction >= inputAge);
        if (selectedFree < 2) {
          this.events[i] = this.events[i].filter(
            (item) => item.data.general.isFree == Boolean(selectedFree)
          );
        }
      }
    },
    moveToMarker(coordinates) {
      console.log(coordinates)
      this.zoomUpdated(19);
      setTimeout(this.centerUpdated, 200, [coordinates[0], coordinates[1]]);
      //this.centerUpdated([coordinates[0], coordinates[1]]);
      
      this.panelVisible = false;
    },
    zoomUpdated (zoom) {
      this.zoom = zoom;
    },
    allEvents() {
      this.panelVisible = true;
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
      this.filterObjectVisible = false;
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
        this.panelVisible = false;
      } catch {
        alert("ошибка");
      }
    },

    closeAside(flag) {
      this.info.flag = flag;
    },

    showFilter() {
      this.filterObjectVisible = true;
    },
    showAnalysis() {
      this.analysisPanelVisible = true;
    },

    getLocation() {

        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.centerLat = position.coords.latitude;
            this.centerLon = position.coords.longitude;
            
            this.center = [this.centerLat, this.centerLon]
            setTimeout(this.centerUpdated, 1000, [this.centerLat, this.centerLon]);
          },
          (error) => {
            console.log(error.message);
          }
        );
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

    async fetchEvents() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/geteventscategory/" +
            this.city +
            "/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    loadingEvents() {
      for (let i = 0; i < 8; i++) {
        this.events[i] = this.parsingEvents.filter(x =>x.data.general.places[0].category.name == this.categoryObject[i]);
      }
    },
  },
  async mounted() {
    this.getLocation();
    
    this.city = await this.postCity(this.centerLat, this.centerLon);
    //Загрузка объектов
    for (let i = 0; i < 8; i++) {
      this.data[i] = await this.fetchData(this.nameCategoryObject[i]);
    }
    //Загрузка событий
    this.parsingEvents = await this.fetchEvents();
    this.loadingEvents();
    //alert("Events");
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
.b1 {
  z-index: 900;
  position: fixed;
  top: 0px;
  left: 100px;
  margin: 10px;
}
.b2 {
  z-index: 900;
  position: fixed;
  top: 0px;
  left: 800px;
  margin: 10px;
}

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
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
  text-align: left;
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
  background-color: black;
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
