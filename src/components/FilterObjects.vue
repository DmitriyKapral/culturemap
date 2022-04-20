<template>
  <form class="filter">
    <div>
      <fieldset @click="closeOut">
        <legend>Фильтры объектов</legend>
        
        <my-select-mult :options="options"
          :selected="selected"
          @select="optionMult"
          v-model:show="showSelect"
          @all="alls"/>
          
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

        <btn class="btn" @click="filterObject">Применить</btn>
      </fieldset>
    </div>
  </form>
</template>

<script>
import Btn from "./UI/Btn.vue";
//import MySelect from './UI/MySelect.vue';
import MySelectMult from './UI/MySelectMult.vue';

export default {
  components: { Btn, /*MySelect*/ MySelectMult},
  emits: ["filterObject"],
  data() {
    return {
      showSelect: false,
      all: [],
      selected: 'Select',
      options: [
        {name: 'Option 1', value: 1},
        {name: 'Option 2', value: 2}
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
    };
  },
  methods: {
    alls(arr){
      this.all = arr;
    },
    filterObject() {
      this.$emit("filterObject", this.selectCategory, this.selectRadius);
    },
    optionSelect(option) {
      this.selected = option.name;
    },
    optionMult(arr) {
      this.all = arr;
      this.showSelect = !this.showSelect;
    },
    closeOut(){
      this.showSelect = false;
    }
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

/* Custom dropdown */
.custom-dropdown {
  position: relative;
  display: inline-block;
  vertical-align: middle;
  margin: 5px; /* demo only */
}

.custom-dropdown select {

  background-color: white;
  color: black;
  font-size: inherit;
  padding: 0.5em;
  padding-right: 2.5em;
  border: 1px solid teal;
  margin: 0;
  border-radius: 3px;
  text-indent: 0.01px;
  text-overflow: "";
  -webkit-appearance: none; /* hide default arrow in chrome OSX */
  outline:none;
}

.custom-dropdown::before,
.custom-dropdown::after {
  content: "";
  position: absolute;
  pointer-events: none;
}

.custom-dropdown::after {
  /*  Custom dropdown arrow */
  content: "\25BC";
  height: 1em;
  font-size: 0.625em;
  line-height: 1;
  right: 1.2em;
  top: 50%;
  margin-top: -0.5em;
}

.custom-dropdown::before {
  /*  Custom dropdown arrow cover */
  width: 2em;
  right: 0;
  top: 0;
  bottom: 0;
  border-radius: 0 3px 3px 0;
}

.custom-dropdown select[disabled] {
  color: teal;
}

.custom-dropdown select[disabled]::after {
  color: teal;
}

.custom-dropdown::before {
  background-color: teal;
}

.custom-dropdown::after {
  color: rgba(0, 0, 0, 0.4);
}
</style>