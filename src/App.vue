<template>
  <div id="map">
    <l-map :zoom="zoom" :center="center" @update:center="centerUpdated">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <div v-for="n in 8" :key="n">
        <get-markers
        v-if="selectedCategory.includes(nameCategoryObject[n-1])"
        :data="data[n-1]"
        :icon="icons[n-1]"
        :events="events[n-1]"
        @getInfo="getData"
      />
      </div>
    </l-map>
    <info-panel
      :info = info
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
            @click="filterEvents"
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
      data: [[], [], [], [], [], [], [], []],
      events: [[], [], [], [], [], [], [], []],
      centerLat: 48.77834045,
      centerLon: 44.77727568,
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 9,
      center: [55.5807, 37.3656],
      markerLatLng: [51.504, -0.159],
      info: 
        {
        flag: false,
        name: "",
        description: "",
        img: "",
        contacts: [],
        workingSchedule: [],
        selectEvents: []
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
      })
      ],
      nameCategoryObject: ["libraries", "cinema", "circuses", "concert_halls", "museums", "parks", "theaters", "culture_palaces_clubs"]
    };
  },
  methods: {
    filterEvents() {
      for (let i = 0; i < this.selectCategoryEvents.length; i++)
      {
        this.filter.push( this.eventsСulturePalacesClubs.filter(data => data.data.general.category.name == this.selectCategoryEvents[i]));
        

      }
      console.log(this.filter);
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
              isFree: this.selectedFree
            }
        );
        return response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },

    async ClickEventsData() {
      for(let i = 0; i < 8; i++)
      {
        this.events[i] = await this.fetchFilterEvents(this.nameCategoryObject[i]);
      }
    },


    async getRadiusData(category) {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/" + this.city + "/",
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
    async ClickData() {
      this.selectedCategory = this.selectCategory;
      for(let i = 0; i<8; i++)
      {
        this.data[i] = await this.getRadiusData(this.nameCategoryObject[i]);
      }
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

    async fetchData(category) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/" + this.city + "/"
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
    
  },
  async mounted() {
    //Переделать
    //await this.getLocation();
    this.city = await this.postCity(this.centerLat, this.centerLon);
    for(let i = 0; i<8; i++)
    {
      this.data[i] = await this.fetchData(this.nameCategoryObject[i]);
    }
    for(let i = 0; i<8; i++)
    {
      this.events[i] = await this.fetchEvents(this.nameCategoryObject[i]);
    }
    alert('Events');

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

.filter_developments {
  position: fixed;
  top: 200vw;
  background-color: #fff;
  z-index: 900;
  top: 0px;
  left: 300px;
  margin: 10px;
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
