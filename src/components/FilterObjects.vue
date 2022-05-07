<template>
  <form class="filter">
    <div>
      <fieldset @click="closeOut">
        <legend>Фильтры объектов</legend>
        <h2>Выберите радиус поиска объектов от своей позиции:</h2>
        <span class="custom-dropdown big">
          <select v-model="selectRadius">
            <option value="500">500 метров</option>
            <option value="1000">1000 метров</option>
            <option value="1500">1500 метров</option>
            <option value="3000">3000 метров</option>
            <option value="100000">Весь город</option>
          </select>
        </span>
        <div class="v-options"></div>
        <h2>Выберите категории поиска объектов:</h2>
        <my-select-mult
          :options="optionsCategory"
          @select="optionMult"
          v-model:show="showSelect"
          @all="closeSelectOut"
          :selectedOptions="selected"
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
//import MySelect from './UI/MySelect.vue';
import MySelectMult from "./UI/MySelectMult.vue";

export default {
  components: { Btn, /*MySelect*/ MySelectMult },
  emits: ["filterObject"],
  props: {
    selected: {
      type: Array,
    }
  },
  data() {
    return {
      showSelect: false,
      all: [],
      options: [
        { name: "Option 1", value: 1 },
        { name: "Option 2", value: 2 },
      ],
      selectRadius: 100000,
      selectCategory: [
        
      ],
      optionsCategory: [
        {
          name: "Библиотеки",
          value: "libraries",
        },
        {
          name: "ДК и клубы",
          value: "culture_palaces_clubs",
        },
        {
          name: "Кинотеатры",
          value: "cinema",
        },
        {
          name: "Цирки",
          value: "circuses",
        },
        {
          name: "Концертные залы",
          value: "concert_halls",
        },
        {
          name: "Музеи",
          value: "museums",
        },
        {
          name: "Парки",
          value: "parks",
        },
        {
          name: "Театры",
          value: "theaters",
        },
      ],
    };
  },
  methods: {
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