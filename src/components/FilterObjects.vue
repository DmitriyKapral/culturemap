<template>
  <form class="filter">
    <div>
      <fieldset @click="closeOut">
        <legend>Фильтры объектов</legend>
        <h2>Выберите радиус поиска объектов от своей позиции:</h2>

        <my-select
          :options="optionsRadius"
          :default="'Весь город'"
          class="select"
          @input="input"
        />

        <div class="v-options"></div>
        <h2>Выберите категории поиска объектов:</h2>
        <my-select-mult
          :options="optionsCategory"
          @select="optionMult"
          v-model:show="showSelect"
          @all="closeSelectOut"
          :selectedOptions="optionsCategory"
        />

        <br />
        <br />

        <btn class="btn" @click="filterObject">Применить</btn>
      </fieldset>
    </div>
  </form>
</template>

<script>
import Btn from "./UI/Btn.vue";
import MySelect from "./UI/MySelect.vue";
import MySelectMult from "./UI/MySelectMult.vue";

export default {
  components: { Btn, MySelectMult, MySelect },
  emits: ["filterObject"],
  data() {
    return {
      showSelect: false,
      all: [],
      optionsRadius: [
        { name: "500 метров", value: 500 },
        { name: "1000 метров", value: 1000 },
        { name: "1500 метров", value: 1500 },
        { name: "3000 метров", value: 3000 },
        { name: "Весь город", value: 100000 },
      ],
      selectRadius: 100000,
      selectCategory: [],
      optionsCategory: [
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
    input(rad) {
      this.selectRadius = rad;
    },
    closeSelectOut(arr) {
      this.selectCategory = arr;
    },
    filterObject() {
      this.$emit("filterObject", this.selectCategory, this.selectRadius);
    },
    optionMult(arr) {
      this.selectCategory = arr;
      this.showSelect = !this.showSelect;
    },
    closeOut() {
      this.showSelect = false;
    },
  },
};
</script>

<style scoped>
.filter {
  background-color: #fff;
  z-index: 1000;
  top: 0px;
  left: 60px;
  margin: 10px;
}
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

.big {
  font-size: 1.2em;
}
body {
  background: #2a2a2b;
  color: #fff;
  text-align: center;
  font-family: Arial, Helvetica;
}
</style>