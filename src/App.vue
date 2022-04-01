<template>
  <div id="map">
    <l-map :zoom="zoom" :center="center" @update:center="centerUpdated">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <get-markers
        v-if="selectedCategory.includes('libraries')"
        :data="dataLibrary"
        :icon="iconLibrary"
        :events="eventsLibrary"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('cinema')"
        :data="dataCinema"
        :icon="iconCinema"
        :events="eventsCinema"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('circuses')"
        :data="dataCircuses"
        :icon="iconCircuses"
        :events="eventsCircuses"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('concert_halls')"
        :data="dataConcert"
        :icon="iconConcert"
        :events="eventsConcert"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('museums')"
        :data="dataMuseums"
        :icon="iconMuseums"
        :events="eventsMuseums"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('parks')"
        :data="dataParks"
        :icon="iconParks"
        :events="eventsParks"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('theaters')"
        :data="dataTheaters"
        :icon="iconTheaters"
        :events="eventsTheaters"
        @getInfo="getData"
      />
      <get-markers
        v-if="selectedCategory.includes('culture_palaces_clubs')"
        :data="dataСulturePalacesClubs"
        :icon="iconСulturePalacesClubs"
        :events="eventsСulturePalacesClubs"
        @getInfo="getData"
      />
    </l-map>
    <info-panel
      :flag="flag"
      :nameinfo="name"
      :description="description"
      :img="img"
      :contacts="contacts"
      :workingSchedule="workingSchedule"
      :selectEvents="selectEvents"
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
          <button @click="ClickData" type="button" class="btn btn-outline-dark">
            Применить
          </button>
        </fieldset>
      </div>
    </form>

    <form class="filter_developments">
      <div>
        <fieldset>
          <legend>Фильтры объектов</legend>
          <select
            class="form-select"
            multiple
            aria-label="multiple select example"
            v-model="selectCategoryEvents"
          >
            <option v-for="category in categoryEvents" v-bind:key="category">
              {{ category }}
            </option>
          </select>
          <br />
          <select v-model="selectedFree">
            <option
              v-for="FreeFalse in freeOrFalse"
              v-bind:key="FreeFalse"
              :value="FreeFalse.value"
            >
              {{ FreeFalse.text }}
            </option>
          </select>
          <br />
          <input v-model="inputAge" type="number" min="0" max="18"/>
          <br />
          <button
            type="button"
            class="btn btn-outline-dark"
            @click="ClickEventsData"
          >
            Применить
          </button>
          <button
            type="button"
            class="btn btn-outline-dark"
          >
            Показать список и применить
          </button>
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
      inputAge: 0,
      selectedFree: 2,
      freeOrFalse: [
        {text: "Да", value: 1},
        {text: "Нет", value: 0},
        {text: "Все", value: 2}
      ],
      categoryEvents: [
        "Встречи",
        "Прочие",
        "Выставки",
        "Концерты",
        "Праздники",
        "Обучение",
        "Спектакли",
        "Кино",
        "Экскурсии"
      ],
      selectCategoryEvents: [
        "Встречи",
        "Прочие",
        "Выставки",
        "Концерты",
        "Праздники",
        "Обучение",
        "Спектакли",
        "Кино",
        "Экскурсии",
      ],
      contacts: [],
      selectEvents: [],
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
      city: "",
      dataLibrary: [],
      dataCinema: [],
      dataCircuses: [],
      dataConcert: [],
      dataMuseums: [],
      dataParks: [],
      dataTheaters: [],
      dataСulturePalacesClubs: [],
      eventsLibrary: [],
      eventsCinema: [],
      eventsCircuses: [],
      eventsConcert: [],
      eventsMuseums: [],
      eventsParks: [],
      eventsTheaters: [],
      eventsСulturePalacesClubs: [],
      dadadadadad: [],
      centerLat: 48.77834045,
      centerLon: 44.77727568,
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
      events: [],

      iconLibrary: icon({
        iconUrl: "/icons/book.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCinema: icon({
        iconUrl: "/icons/cinema.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCircuses: icon({
        iconUrl: "/icons/circuses.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconConcert: icon({
        iconUrl: "/icons/concert.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconMuseums: icon({
        iconUrl: "/icons/museum.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconParks: icon({
        iconUrl: "/icons/parks.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconTheaters: icon({
        iconUrl: "/icons/theaters.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconСulturePalacesClubs: icon({
        iconUrl: "/icons/culturepalacesclubs.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
    };
  },
  methods: {
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
              isFree: this.selectedFree
            }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    async ClickEventsData() {
      this.eventsLibrary = await this.fetchFilterEvents(
        "libraries"
      );
      this.eventsCinema = await this.fetchFilterEvents(
        "cinema"
      );
      this.eventsCircuses = await this.fetchFilterEvents(
        "circuses"
      );
      this.eventsConcert = await this.fetchFilterEvents(
        "concert_halls"
      );
      this.eventsMuseums = await this.fetchFilterEvents(
        "museums"
      );
      this.eventsParks = await this.fetchFilterEvents(
        "parks"
      );
      this.eventsTheaters = await this.fetchFilterEvents(
        "theaters"
      );
      this.eventsСulturePalacesClubs = await this.fetchFilterEvents(
        "culture_palaces_clubs"
      );
      alert("ОК")
    },


    //Очень плохо реальзованный фильтр, но рабочий)
    async getRadiusData(category, lat, long, radius) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/" + this.city + "/",
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
      this.dataLibrary = await this.getRadiusData(
        "libraries",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataCinema = await this.getRadiusData(
        "cinema",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataCircuses = await this.getRadiusData(
        "circuses",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataConcert = await this.getRadiusData(
        "concert_halls",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataMuseums = await this.getRadiusData(
        "museums",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataParks = await this.getRadiusData(
        "parks",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataTheaters = await this.getRadiusData(
        "theaters",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
      this.dataСulturePalacesClubs = await this.getRadiusData(
        "culture_palaces_clubs",
        this.centerLat,
        this.centerLon,
        this.selectRadius
      );
    },
    /*async testEvents() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/testevent/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },*/

    async fetchData(category, city) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/" + city + "/"
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
        this.selectEvents = data.filterEvents;
      } catch {
        alert("ошибка");
      }
    },

    closeAside(flag) {
      this.flag = flag;
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

    async fetchEvents(category, city) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/geteventscategory/" +
            category +
            "/" +
            city +
            "/"
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    
  },
  async mounted() {
    //Переделать
    //await this.getLocation();
    this.city = await this.postCity(this.centerLat, this.centerLon);
    this.dataLibrary = await this.fetchData("libraries", this.city);
    this.dataCinema = await this.fetchData("cinema", this.city);
    this.dataCircuses = await this.fetchData("circuses", this.city);
    this.dataConcert = await this.fetchData("concert_halls", this.city);
    this.dataMuseums = await this.fetchData("museums", this.city);
    this.dataParks = await this.fetchData("parks", this.city);
    this.dataTheaters = await this.fetchData("theaters", this.city);
    this.dataСulturePalacesClubs = await this.fetchData(
      "culture_palaces_clubs",
      this.city
    );
    this.eventsLibrary = await this.fetchEvents("libraries", this.city);
    this.eventsCinema = await this.fetchEvents("cinema", this.city);
    this.eventsCircuses = await this.fetchEvents("circuses", this.city);
    this.eventsConcert = await this.fetchEvents("concert_halls", this.city);
    this.eventsMuseums = await this.fetchEvents("museums", this.city);
    this.eventsParks = await this.fetchEvents("parks", this.city);
    this.eventsTheaters = await this.fetchEvents("theaters", this.city);
    this.eventsСulturePalacesClubs = await this.fetchEvents(
      "culture_palaces_clubs",
      this.city
    );

    //this.events = await this.testEvents();

    //console.log(this.testEvent.filter(data => data.data.general.places[0].id == 8201))
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
.filter_developments {
  position: fixed;
  top: 200vw;
  background-color: #fff;
  z-index: 900;
  top: 0px;
  left: 300px;
  margin: 10px;
}

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>
