<template>
  <div>
    <button @click="getChart">Клик</button>
    <apexchart
      width="500"
      type="bar"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";
import axios from "axios";
export default {
  components: {
    apexchart: VueApexCharts,
  },
  data() {
    return {
      data: [],
      count: [],
      month: [],
      chartOptions: {
        xaxis: {
          categories: [19921, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
        },
      },
      series: [
        {
          name: 'Количество',
          data: [1, 2, 3, 4, 1, 2, 3, 5],
        },
      ],
    };
  },
  methods: {
    async getChart() {
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/getcountevents/Спектакли/Волжский/"
        );
        this.count = []
        this.month = []
        this.data = response.data;
        for (let i = 11; i >= 0; i--) {
          this.count.push(this.data[i].count);
          this.month.push(this.data[i].month);
        }
        this.chartOptions = {
          xaxis: {
            categories: this.month,
          },
        }

        this.series = [{
            data: this.count,
          }];
        alert("awwd");
      } catch (a) {
        alert("Ошибка");
      }
    },
  },
  
  
};
</script>

<style lang="scss" scoped>
</style>