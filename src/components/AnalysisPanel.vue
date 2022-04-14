<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !show }">
      <button class="object__close" @click="closeAside"></button>
      <div>
        <select v-model="selectedCategory">
          <option disabled value="">Выберите один из вариантов</option>
          <option v-for="category in categoryEvents" :key="category">
            {{ category }}
          </option>
        </select>
        <button @click="getChart">Клик</button>
        <apexchart
          width="650"
          type="bar"
          :options="chartOptions"
          :series="series"
        ></apexchart>
      </div>
    </aside>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";
import axios from "axios";
export default {
  components: {
    apexchart: VueApexCharts,
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
      data: [],
      count: [],
      month: [],
      chartOptions: {
        xaxis: {
          categories: [0, 0, 0, 0, 0, 0, 0, 0],
        },
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
        alert("awwd");
      } catch (a) {
        alert("Ошибка");
      }
    },
  },
};
</script>

<style scoped>
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>