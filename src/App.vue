<template>
  <div id="map">
    <l-map :zoom="zoom" :center="[centerLat, centerLon]">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <get-markers :data="dataLibrary" :icon="iconLibrary" @getInfo="getData" />
      <get-markers :data="dataCinema" :icon="iconCinema" @getInfo="getData" />
      <get-markers
        :data="dataCircuses"
        :icon="iconCircuses"
        @getInfo="getData"
      />
      <get-markers :data="dataConcert" :icon="iconConcert" @getInfo="getData" />
      <get-markers :data="dataMuseums" :icon="iconMuseums" @getInfo="getData" />
      <get-markers :data="dataParks" :icon="iconParks" @getInfo="getData" />
      <get-markers
        :data="dataTheaters"
        :icon="iconTheaters"
        @getInfo="getData"
      />
      <get-markers :data="dataСulturePalacesClubs" :icon="iconСulturePalacesClubs" @getInfo="getData" />
    </l-map>
    <info-panel
      :flag="flag"
      :name="name"
      :description="description"
      :img="img"
      @closeAside="closeAside"
    />
    <form class="filter">
    <div>
      <fieldset>
        <legend>Фильтры объектов</legend>
        <div class="v-options"></div>
        
        <select>
          <option>500 м</option>
          <option>1000 м</option>
          <option>1500 м</option>
          <option>3000 м</option>
          <option>Весь город</option></select
        ><br />
        <select multiple>
          <option>Библиотеки</option>
          <option>Кинотеатры</option>
          <option>Цирки</option>
          <option>Концертные залы</option>
          <option>Музеи</option>
          <option>Парки</option>
          <option>Театры</option>
        </select>
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
      options: [
        {
          name: "1"
        },
        {
          name: "2"
        }
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
          "https://psv4.userapi.com/c237331/u191787332/docs/d59/9da5b3c854f7/book.png?extra=kRxn3C3mQmXtfL2vRNVeIXYJ-rLfcmii4Pp_Oov92f7X7WIAmT1mr05Vq22eCY26dr30pVsC6E0ESIV7pcDIaQhNejRJP4GOS03wkC0d5EN4T3wxcB9nMMhzkcHv3cAfOhmHYTxu7YJrlHSj5eL067D8yA",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCinema: icon({
        iconUrl:
          "https://psv4.userapi.com/c537232/u191787332/docs/d31/88b8c9663660/6382213.png?extra=QPb5d03GpMcf1nlAyc5rQmLhSYBIMZ6bs9rTyuN1nssCXe765Jt7aDInznWJUM0mGx158pd_K8XI3aXa0DxYtv1eYWNdyasb-_q511ITTTPklMlFvQDH_otEX9Awq6R_Eqbnh784UX5OoS2NmZBVUYyw",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconCircuses: icon({
        iconUrl:
          "https://psv4.userapi.com/c537232/u191787332/docs/d22/816759678554/895305.png?extra=4BQ0okQPhiba-KFTFeTs3paoV350bCRlEirYCJVhD3WQvmlHbDrBgyqtNe_qJqoHb5oVVgYbPqwSG2qVKLz3ywkdTu0m1y25liE0KGmuijsrn8OLZk4KvHlQ7ds-QP98fAAT68UbfIraBJG8L8qwkhWJuQ",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconConcert: icon({
        iconUrl:
          "https://psv4.userapi.com/c237231/u191787332/docs/d53/9e64cf186c1d/1776597.png?extra=ZH5xSQ9JplKqhMuCiRx2mU4V6vdFEn0pQDYfRoYi0ZmrBLg_hIxgzTb7zDVlpIZALfdRgCdowtTuEjzO8uRxRxOCiS7C7TcDZ6RlLsTGBDjoYzDtOS4bzWvielbGULIJB-Vud2ejReUXUTApZ4Kw8EtXSg",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconMuseums: icon({
        iconUrl:
          "https://psv4.userapi.com/c235131/u191787332/docs/d33/81d9212ac2ac/3270984.png?extra=nxDuQV_kF8Lj6qf2t_rTVks2VslFm5cDZraJdjHJ3xWiMng7i-TKwcKXJXIF6Wx5ANESJKxaZdsH76ZdgzxrQuGtt_45jSvgTa7f56V1CRoDoYFyQnnOsOnvjATMCC8RhL_qLjS0cXXbDfypW20SKOn1BA",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconParks: icon({
        iconUrl:
          "https://psv4.userapi.com/c237031/u191787332/docs/d49/8383f8a16ff0/291445.png?extra=FQZqGXQh7rfAAOc2JAI0ZdEfSIgtQDfGtBJF_vrzBTnHwe8Yoi3j59sprDlhtSAd87J9OYjDwXS-VP-A_ADVQFoPqujrpCuQodDIRA5tkftXNrqPeIMdiBdiIHmzmZR5P1WUrQonusVEvIfLFNKJopT47g",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconTheaters: icon({
        iconUrl:
          "https://psv4.userapi.com/c237131/u191787332/docs/d34/32ee4c35c84a/4318583.png?extra=raY5TYIlb5DXtnekXkHQBkXjWcl3eDDmU_wYikbK9pEIvAHylKqv0ENlrm0q1AfRJ043pfYuNqEmTDPTM35w4SZxRVlQAKXlVE2OX9KaNM3QCVpVlWjT6ACqAH-zbB9X7NZXZhn12sigBDviuiB0FdDEyQ",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
      iconСulturePalacesClubs: icon({
        iconUrl:
          "https://psv4.userapi.com/c536436/u191787332/docs/d17/526638b0f409/2314735.png?extra=c77iHEMF-gHerIjZQvC1xIkzswpkC3hPsbAqQ2jatl0XpaMo9v67NjL3OSJzX6u0yRAt9IZ8DBV2aoHVZgd63PEcU4ixu9n0ssFHwV4y5tyRMnK2LAhX8sRDBNQSNOLxHo1TqANuKEwc8qt1YgwRhOYO",
        iconSize: [32, 37],
        iconAnchor: [16, 37],
      }),
    };
  },
  methods: {
    async fetchData(category) {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/categoryget/" + category + "/Волгоград/"
        );
        return(response.data)
      } catch (a) {
        alert("Ошибка");
      }
    },
    async getPosition() {
      try {
        const response = await axios.get(
          "https://nominatim.openstreetmap.org/reverse?format=json&lat=48.7784448&lon=44.777472"
        );

        this.dadadadadad = JSON.stringify(response);
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
    locationButtonPressed() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.centerLat = position.coords.latitude;
            this.centerLon = position.coords.longitude;
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
    this.dataСulturePalacesClubs = await this.fetchData("culture_palaces_clubs");
    await this.getPosition();
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
