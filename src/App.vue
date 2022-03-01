<template>
  <div id="map">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <get-markers :data="dataLibrary" :icon="iconLibrary" @getInfo="getData" />
      <get-markers :data="dataCinema" :icon="iconCinema" @getInfo="getData" />
      <get-markers :data="dataCircuses" :icon="iconCircuses" @getInfo="getData" />
      <get-markers :data="dataConcert" :icon="iconConcert" @getInfo="getData" />
      <get-markers :data="dataMuseums" :icon="iconMuseums" @getInfo="getData" />
      <get-markers :data="dataParks" :icon="iconParks" @getInfo="getData" />
      <get-markers :data="dataTheaters" :icon="iconTheaters" @getInfo="getData" />
    </l-map>
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
//import MyDialog from "MyDialog.vue"
import axios from "axios";
import { icon } from "leaflet";
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer } from "@vue-leaflet/vue-leaflet";
import GetMarkers from "./components/GetMarkers.vue";

export default {
  name: "App",
  components: { LMap, LTileLayer, GetMarkers },
  data() {
    return {
      dataLibrary: [],
      dataCinema: [],
      dataCircuses: [],
      dataConcert: [],
      dataMuseums: [],
      dataParks: [],
      dataTheaters: [],
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
        iconUrl: "https://psv4.userapi.com/c237331/u191787332/docs/d59/9da5b3c854f7/book.png?extra=kRxn3C3mQmXtfL2vRNVeIXYJ-rLfcmii4Pp_Oov92f7X7WIAmT1mr05Vq22eCY26dr30pVsC6E0ESIV7pcDIaQhNejRJP4GOS03wkC0d5EN4T3wxcB9nMMhzkcHv3cAfOhmHYTxu7YJrlHSj5eL067D8yA",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconCinema: icon({
        iconUrl: "https://psv4.userapi.com/c537232/u191787332/docs/d31/ebbf7072ef2f/6382213.png?extra=QoijFkaxR_YASWVcudcywJ1gsvRQBIEd1jvSUepti0U1TCFlZESdzH0K9UdJdgAterk8lZQYgjZzhwe13gSqr8iCL7rlEdj-mp6CI00WWiwVm9LxyoKNAJeCKqvJ4KUMUkNqvR7nLvqjfuGETTvDZovrDA",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconCircuses: icon({
        iconUrl: "https://psv4.userapi.com/c537232/u191787332/docs/d22/816759678554/895305.png?extra=4BQ0okQPhiba-KFTFeTs3paoV350bCRlEirYCJVhD3WQvmlHbDrBgyqtNe_qJqoHb5oVVgYbPqwSG2qVKLz3ywkdTu0m1y25liE0KGmuijsrn8OLZk4KvHlQ7ds-QP98fAAT68UbfIraBJG8L8qwkhWJuQ",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconConcert: icon({
        iconUrl: "https://psv4.userapi.com/c237231/u191787332/docs/d53/9e64cf186c1d/1776597.png?extra=ZH5xSQ9JplKqhMuCiRx2mU4V6vdFEn0pQDYfRoYi0ZmrBLg_hIxgzTb7zDVlpIZALfdRgCdowtTuEjzO8uRxRxOCiS7C7TcDZ6RlLsTGBDjoYzDtOS4bzWvielbGULIJB-Vud2ejReUXUTApZ4Kw8EtXSg",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconMuseums: icon({
        iconUrl: "https://psv4.userapi.com/c235131/u191787332/docs/d33/81d9212ac2ac/3270984.png?extra=nxDuQV_kF8Lj6qf2t_rTVks2VslFm5cDZraJdjHJ3xWiMng7i-TKwcKXJXIF6Wx5ANESJKxaZdsH76ZdgzxrQuGtt_45jSvgTa7f56V1CRoDoYFyQnnOsOnvjATMCC8RhL_qLjS0cXXbDfypW20SKOn1BA",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconParks: icon({
        iconUrl: "https://psv4.userapi.com/c237031/u191787332/docs/d49/8383f8a16ff0/291445.png?extra=FQZqGXQh7rfAAOc2JAI0ZdEfSIgtQDfGtBJF_vrzBTnHwe8Yoi3j59sprDlhtSAd87J9OYjDwXS-VP-A_ADVQFoPqujrpCuQodDIRA5tkftXNrqPeIMdiBdiIHmzmZR5P1WUrQonusVEvIfLFNKJopT47g",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconTheaters: icon({
        iconUrl: "https://psv4.userapi.com/c237131/u191787332/docs/d34/32ee4c35c84a/4318583.png?extra=raY5TYIlb5DXtnekXkHQBkXjWcl3eDDmU_wYikbK9pEIvAHylKqv0ENlrm0q1AfRJ043pfYuNqEmTDPTM35w4SZxRVlQAKXlVE2OX9KaNM3QCVpVlWjT6ACqAH-zbB9X7NZXZhn12sigBDviuiB0FdDEyQ",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
    };
  },
  methods: {
    async fetchDataLibrary() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/alllibrary/"
        );
        this.dataLibrary = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataCinema() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/allcinema/"
        );
        this.dataCinema = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataCircuses() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/allcircuses/"
        );
        this.dataCircuses = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataConcert() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/allconcert/"
        );
        this.dataConcert = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataMuseums() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/allmuseums/"
        );
        this.dataMuseums = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataParks() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/allparks/");
        this.dataParks = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataTheaters() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/alltheaters/"
        );
        this.dataTheaters = response.data;
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
      } catch {
        alert("ошибка");
      }
    },
    closeAside() {
      this.flag = false;
    },
    locationButtonPressed() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            console.log(position.coords.latitude);
            console.log(position.coords.longitude);
          },
          (error) => {
            console.log(error.message);
          }
        );
      } else {
        console.log("Your browser does not suppert geolocation API");
      }
    },
  },
  async mounted() {
    await this.fetchDataLibrary();
    await this.fetchDataCinema();
    await this.fetchDataCircuses();
    await this.fetchDataConcert();
    await this.fetchDataMuseums();
    await this.fetchDataParks();
    await this.fetchDataTheaters();
    /*let map;
    map = leaflet.map("map").setView([55.5807, 37.3656], 10);

    leaflet
      .tileLayer("http://{s}.tile.osm.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      })
      .addTo(map);*/
    //this.data[i].data.general.name
    /*for (let i = 0; i < this.data.length; i++) {
      leaflet
        .marker([
          Number(this.data[i].data.general.address.mapPosition.coordinates[1]),
          Number(this.data[i].data.general.address.mapPosition.coordinates[0]),
        ])
        .addTo(map);
    }*/
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
