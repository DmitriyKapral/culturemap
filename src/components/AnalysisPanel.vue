<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !show }">
      <button class="object__close" @click="closeAside"></button>
      <h1>Анализ прошедших мероприятий по категориям:</h1>
      <my-select
        style="width: 300px; left: -5px; margin: 5px;"
        :options="categoryEvents"
        :default="'Выберите один из вариантов'"
        class="select"
        @input="input"
      />
      
      <btn class="btn" @click="getChart">Построить график</btn>
      <h1>Показать количество объектов культуры в текущем городе:</h1>
      <btn class="btn" @click="getCountObjects">Построить график</btn>
      <apexchart
        width="650"
        type="bar"
        :options="chartOptions"
        :series="series"
      ></apexchart>
    </aside>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";
import axios from "axios";
import Btn from './UI/Btn.vue';
import MySelect from "./UI/MySelect.vue";
export default {
  components: {
    apexchart: VueApexCharts,
    Btn,
    MySelect
},
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    city: {
      type: String,
    },
  },
  data() {
    return {
      selectedCategory: "",
      
      categoryEvents: [
        { name: "Встречи", value: "Встречи" },
        { name: "Прочие", value: "Прочие" },
        { name: "Выставки", value: "Выставки" },
        { name: "Концерты", value: "Концерты" },
        { name: "Праздники", value: "Праздники" },
        { name: "Обучение", value: "Обучение" },
        { name: "Спектакли", value: "Спектакли" },
        { name: "Кино", value: "Кино" },
        { name: "Экскурсии", value: "Экскурсии" },
      ],
      data: [],
      count: [],
      month: [],
      chartOptions: {
        xaxis: {
          categories: [0, 0, 0, 0, 0, 0, 0, 0],
        },
        //colors: "#008080"
      },
      series: [
        {
          name: "Количество",
          data: [0, 0, 0, 0, 0, 0, 0, 0],
        },
      ],
    };
  },
  methods: {
    input(select){
      this.selectedCategory = select;
    },
    closeAside() {
      this.$emit("update:show", false);
    },
    async getChart() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/getcountevents/" +
            this.selectedCategory +
            "/" +
            this.city +
            "/"
        );
        this.count = [];
        this.month = [];
        this.data = response.data;
        for (let i = 11; i >= 0; i--) {
          this.count.push(this.data[i].count);
          this.month.push(this.data[i].month);
        }
        this.chartOptions = {
          xaxis: {
            categories: this.month,
          },
        };

        this.series = [
          {
            data: this.count,
          },
        ];
        //alert("awwd");
      } catch (a) {
        alert("Ошибка");
      }
    },

    async getCountObjects(){
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/getcountobjects/" +
            this.city +
            "/"
        );
        this.chartOptions = {
          xaxis: {
            categories: response.data.name,
          },
        };

        this.series = [
          {
            data: response.data.count,
          },
        ];
        } catch (a) {
        alert("Ошибка");
      }
    }
  },
};
</script>

<style scoped>
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>