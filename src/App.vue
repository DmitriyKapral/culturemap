<template>
  <div id="map" >
    <l-map
      :zoom="zoom"
      :center="center"
      @update:center="centerUpdated"
      @update:zoom="zoomUpdated"
    >
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <div v-for="n in 8" :key="n">
        <get-markers
          v-if="selectedCategory.includes(categoryObject[n - 1])"
          :data="data[n - 1]"
          :icon="icons[n - 1]"
          :events="events[n - 1]"
          @getInfo="getData"
        />
      </div>
      <div>
        <l-marker
        v-model:lat-lng="markerLatLng"
        :draggable="true"
        >
          <l-popup>Вы находитесь здесь!</l-popup>
        </l-marker>
      </div>
      
    </l-map>
    
    <info-panel :info="info" @closeAside="closeAside" />
    <events-panel
      :events="events"
      @moveToMarker="moveToMarker"
      v-model:show="panelVisible"
    />

    <my-filter v-model:show="filterObjectVisible">
      <div class="pos">
        <button
          v-for="tab in tabs"
          v-bind:key="tab"
          v-bind:class="['tab-button', { active: currentTab === tab }]"
          v-on:click="currentTab = tab"
        >
          {{ tab }}
        </button>
        <component v-bind:is="currentTabComponent" class="tab"></component>
      </div>
      <filter-objects
        @filterObject="filterObject"
        v-if="!filterVisible"
        :selected="selectedCategory"
      />
      <filter-events
        @filterEvents="filterEvents"
        @allEvents="allEvents"
        v-if="filterVisible"
      />
    </my-filter>
    <div class="fixed">
      <div class="search">
        <div class="d1">
          <input
            type="search"
            v-on:keyup="validateSearch"
            v-model="search"
            placeholder="Искать здесь..."
          />
          <button type="button" @click="SearchToName"></button>
        </div>
      </div>
      <div class="b1">
        <btn class="btn" @click="showFilter">Фильтры</btn>
      </div>
      <div class="b2">
        <btn class="btn" @click="showAnalysis">Анализ</btn>
      </div>
    </div>

    <analysis-panel :city="city" v-model:show="analysisPanelVisible" />
  </div>
</template>

<script>
//import MyDialog from "MyDialog.vue"
import axios from "axios";
import { icon } from "leaflet";
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LMarker, LPopup } from "@vue-leaflet/vue-leaflet";
import GetMarkers from "./components/GetMarkers.vue";
import InfoPanel from "./components/InfoPanel.vue";
import FilterObjects from "./components/FilterObjects.vue";
import FilterEvents from "./components/FilterEvents.vue";
import MyFilter from "./components/UI/MyFilter.vue";
import EventsPanel from "./components/EventsPanel.vue";
import AnalysisPanel from "./components/AnalysisPanel.vue";
import Btn from "./components/UI/Btn.vue";

export default {
  name: "App",
  components: {
    checkMovingMarker: true,
    LMap,
    LTileLayer,
    GetMarkers,
    InfoPanel,
    FilterObjects,
    FilterEvents,
    MyFilter,
    EventsPanel,
    AnalysisPanel,
    Btn,
    LMarker,
    LPopup
  },
  data() {
    return {
      search: "",
      filterVisible: false,
      currentTab: "Фильтр Объектов",
      tabs: ["Фильтр Объектов", "Фильтр Мероприятий"],
      analysisPanelVisible: false,
      panelVisible: false,
      filterObjectVisible: false,
      selectCategoryEvents: [],
      selectedFree: 0,
      inputAge: 0,
      selectedCategory: [
        "Библиотеки",
        "Кинотеатры",
        "Цирки",
        "Концертные залы",
        "Музеи и галереи",
        "Парки",
        "Театры",
        "ДК и клубы",
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
        address: "",
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
      categoryObject: [
        "Библиотеки",
        "Кинотеатры",
        "Цирки",
        "Концертные залы",
        "Музеи и галереи",
        "Парки",
        "Театры",
        "ДК и клубы",
      ],
    };
  },
  methods: {
    someMethod(event) {
        // clientX/Y gives the coordinates relative to the viewport in CSS pixels.
        const a = event.latlng?.lat;
        console.log(a)

    },
    SearchToName() {
      if (this.search == "") {
        return;
      } else {
        for (let i = 0; i < 8; i++) {
          const searchName = this.data[i].find((x) =>
            x.data.general.name
              .toLowerCase()
              .includes(this.search.toLowerCase())
          );
          if (typeof searchName != "undefined") {
            this.zoomUpdated(19);
            setTimeout(this.centerUpdated, 200, [
              searchName.data.general.address.mapPosition.coordinates[1],
              searchName.data.general.address.mapPosition.coordinates[0],
            ]);
            break;
          }
        }
      }
    },
    validateSearch: function (e) {
      if (e.keyCode === 13) {
        this.SearchToName();
      }
    },
    currentTabComponent() {
      if (this.currentTab == "Фильтр Объектов") {
        this.filterVisible = false;
      } else {
        this.filterVisible = true;
      }
    },
    filterEvents(selectCategoryEvents, selectedFree, inputAge, mydate) {
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
        if (!mydate.trim() == '')
        {
          this.events[i] = this.events[i].filter(event => event.data.general.seances.some(date => date.start.includes(mydate)));
        }
      }
    },
    moveToMarker(coordinates) {
      console.log(coordinates);
      this.zoomUpdated(19);
      setTimeout(this.centerUpdated, 200, [coordinates[0], coordinates[1]]);
      //this.centerUpdated([coordinates[0], coordinates[1]]);

      this.panelVisible = false;
    },
    zoomUpdated(zoom) {
      this.zoom = zoom;
    },
    allEvents() {
      this.filterObjectVisible = false;
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
            lat: this.markerLatLng.lat,
            long: this.markerLatLng.lng,
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
        this.info.address = data.address;
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

          this.markerLatLng = {lat: this.centerLat, lng: this.centerLon};
          setTimeout(this.centerUpdated, 1000, [
            this.centerLat,
            this.centerLon,
          ]);
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
          "http://127.0.0.1:8000/api/geteventscategory/" + this.city + "/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    loadingEvents() {
      for (let i = 0; i < 8; i++) {
        this.events[i] = this.parsingEvents.filter(
          (x) =>
            x.data.general.places[0].category.name == this.categoryObject[i]
        );
      }
    },
  },
  async mounted() {
    this.getLocation();
    this.city = await this.postCity(this.centerLat, this.centerLon);
    //this.markerLatLng = this.center;
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

  top: 0px;
  left: 18%;
  margin: 1%;
}
.b2 {
  z-index: 900;

  top: 0px;
  left: 27%;
  margin: 1%;
}
.search {
  z-index: 900;
  top: 0px;
  left: 3%;
  margin: 1%;

}
.fixed {
  position: fixed;
  display: flex;
  width: 1600px;
  height: 100px;
  z-index: 900;
  left: 4%;
  top: 0;


}

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  font-family: "Times New Roman";
  font-size: 19px;
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

.tab-button:hover {
  background: #e0e0e0;
}
.tab-button.active {
  background: #e0e0e0;
}
.tab-button {
  padding: 6px 20px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  position: relative;
  margin-right: 0px;
  width: 300px;
  left: 0;
}

.d1 {
  display: flex;
}
.d1 input {
  width: 100%;
  height: 42px;
  padding-left: 10px;
  border: 2px solid teal;
  border-radius: 5px;
  outline: none;
  background: white;
  color: black;
}
.d1 button {
  margin-left: -20px;
  top: 0;
  right: 0px;
  width: 42px;
  height: 42px;
  border: none;
  background: teal;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
}
.d1 button:active {
  background: rgb(6, 185, 185);
}
.d1 button:before {
  content: "\f002";
  font-family: FontAwesome;
  font-size: 16px;
  color: white;
}
/* Custom dropdown */
.custom-dropdown {
  position: relative;
  display: inline-block;
  vertical-align: middle;
  margin: 5px; /* demo only */
}

.custom-dropdown select {
  background-color: white;
  cursor: pointer;
  color: black;
  font-size: inherit;
  padding: 0.5em;
  padding-right: 2.5em;
  border: 1px solid teal;
  margin: 0;
  border-radius: 3px;
  text-indent: 0.01px;
  text-overflow: "";
  -webkit-appearance: none; /* hide default arrow in chrome OSX */
  outline: none;
}

.custom-dropdown::before,
.custom-dropdown::after {
  content: "";
  position: absolute;
  pointer-events: none;
}

.custom-dropdown::after {
  /*  Custom dropdown arrow */
  content: "\25BC";
  height: 1em;
  font-size: 0.625em;
  line-height: 1;
  right: 1.2em;
  top: 50%;
  margin-top: -0.5em;
}

.custom-dropdown::before {
  /*  Custom dropdown arrow cover */
  width: 2em;
  right: 0;
  top: 0;
  bottom: 0;
  border-radius: 0 3px 3px 0;
}

.custom-dropdown select[disabled] {
  color: teal;
}

.custom-dropdown select[disabled]::after {
  color: teal;
}

.custom-dropdown::before {
  background-color: teal;
}

.custom-dropdown::after {
  color: rgba(0, 0, 0, 0.4);
}

.table {
	width: 100%;
	margin-bottom: 20px;
	border: 5px solid #fff;
	border-top: 5px solid #fff;
	border-bottom: 3px solid #fff;
	border-collapse: collapse; 
	font-size: 15px;

}
.table th {
	font-weight: bold;
	padding: 7px;
	background: white;
	border: none;
	text-align: left;
	font-size: 15px;
	border-top: 3px solid teal;
	border-bottom: 3px solid teal;
}
.table td {
	padding: 7px;
	border: none;
	border-top: 3px solid #fff;
	border-bottom: 3px solid teal;
	font-size: 15px;
}


</style>
