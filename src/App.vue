<template>
  <div id="map">
    
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <div v-for="item in dataLibrary" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconLibrary"
          @click="getData(item)"
        ></l-marker>
      </div>
      <div v-for="item in dataCinema" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconCinema"
          @click="getData(item)"
        ></l-marker>
      <div v-for="item in dataCircuses" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconCircuses"
          @click="getData(item)"
        ></l-marker>
      </div>
      <div v-for="item in dataConcert" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconConcert"
          @click="getData(item)"
        ></l-marker>
      </div>
      <div v-for="item in dataMuseums" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconMuseums"
          @click="getData(item)"
        ></l-marker>
      </div>

      <div v-for="item in dataParks" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconParks"
          @click="getData(item)"
        ></l-marker>
      </div>

      <div v-for="item in dataTheaters" v-bind:key="item._id">
        <l-marker
          :lat-lng="[
            item.data.general.address.mapPosition.coordinates[1],
            item.data.general.address.mapPosition.coordinates[0],
          ]"
          :icon="iconTheaters"
          @click="getData(item)"
        ></l-marker>
      </div>

      </div>
      
    </l-map>
    <aside class="object" v-bind:class="{'object_closed': !flag}">
      <button class="object__close" @click="closeAside"></button>
      <button @click="locationButtonPressed">Нажми на меня</button>
      <div class="object__title"></div>
        <h2>{{name}}</h2>
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
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";
import { icon } from "leaflet";

export default {
  name: "App",
  components: { LMap, LTileLayer, LMarker },
  data() {
    return {
      dataLibrary: [],
      dataCinema: [],
      dataCircuses: [],
      dataConcert: [],
      dataMuseums: [],
      dataParks: [],
      dataTheaters: [],
      name: "hi",
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 9,
      center: [55.5807, 37.3656],
      markerLatLng: [51.504, -0.159],
      flag: false,
      description: '',
      img: '',
      iconLibrary: icon({
        iconUrl: "https://cdn-icons.flaticon.com/png/512/5832/premium/5832416.png?token=exp=1646073975~hmac=ebd76ab23e896dd0771060c48464e732",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconCinema: icon({
        iconUrl: "https://cdn-icons.flaticon.com/png/512/1655/premium/1655698.png?token=exp=1646074947~hmac=aa5b98535f34b6ceab365335204f87be",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconCircuses: icon({
        iconUrl: "https://cdn-icons.flaticon.com/png/512/895/premium/895305.png?token=exp=1646074979~hmac=58c19273bba088f7e69cb9962d10d829",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconConcert: icon({
        iconUrl: "https://cdn-icons-png.flaticon.com/512/1776/1776597.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconMuseums: icon({
        iconUrl: "https://cdn-icons-png.flaticon.com/512/6767/6767145.png",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconParks: icon({
        iconUrl: "https://cdn-icons.flaticon.com/png/512/4104/premium/4104488.png?token=exp=1646075045~hmac=58cbef627735c17a0935b1a58fbe5e16",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
      iconTheaters: icon({
        iconUrl: "https://cdn-icons.flaticon.com/png/512/4539/premium/4539424.png?token=exp=1646075076~hmac=9962180b3ccc23daf2b7ce171a9ce3c5",
        iconSize: [32, 37],
        iconAnchor: [16, 37]
      }),
    };
  },
  methods: {
    async fetchDataLibrary() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/alllibrary/");
        this.dataLibrary = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataCinema() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/allcinema/");
        this.dataCinema = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataCircuses() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/allcircuses/");
        this.dataCircuses = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataConcert() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/allconcert/");
        this.dataConcert = response.data;
      } catch (a) {
        alert("Ошибка");
      }
    },
    async fetchDataMuseums() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/allmuseums/");
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
        const response = await axios.get("http://127.0.0.1:8000/api/alltheaters/");
        this.dataTheaters = response.data;

      } catch (a) {
        alert("Ошибка");
      }
    },
    getData(data) {
      try {
        this.flag = true;
        this.name = data.data.general.name;
        this.description = data.data.general.description;
        this.img = data.data.general.image.url;
        //alert(this.name);
      } catch {
        alert("ошибка");
      }
    },
    closeAside() {
      this.flag = false;
    },
    locationButtonPressed() {
      if(navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          position => {
            console.log(position.coords.latitude);
            console.log(position.coords.longitude);
          },
          error => {
            console.log(error.message);
          }
        );
      }
      else {
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
    console.log(this.data);
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
  -webkit-transition: .6s;
  -o-transition: .6s;
  transition: .6s;
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
  content: '';
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
