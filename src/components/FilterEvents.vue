<template>
  <form class="filter_developments">
    <div>
      <fieldset @click="closeOut">
        <legend>Фильтры событий</legend>
        <strong>Выберите категории поиска мероприятий:</strong> <br>
        <my-select-mult
          :options="categoryEvents"
          @select="optionMult"
          v-model:show="showSelect"
          @all="closeSelectOut"
          :selectedOptions="selectCategoryEvents"
        />
        <br>
        <strong>Поиск по платным мероприятиям:</strong> <br>
        <span class="custom-dropdown big">
          <select v-model="selectedFree">
            <option
            class="decorated"
              v-for="FreeFalse in freeOrFalse"
              v-bind:key="FreeFalse"
              :value="FreeFalse.value"
            >
              {{ FreeFalse.text }}
            </option>
          </select>
        </span>
        <br />
        <strong>Выберите ограничение по возврасту: </strong> <br>
        <div class="number">
          <button class="number-minus" type="button" @click="minusInput">
            -
          </button>
          <input v-model="inputAge" type="number" min="0" max="18" readonly/>
          <button
            class="number-plus"
            type="button"
            @click="plusInput"
          >
            +
          </button>
        </div>
        <br />
        <strong>Выберите дату начала мероприятий:</strong><br />
        <input class="date" :min="new Date().toISOString().split('T')[0]" type="date" v-model="mydate" />
        <br />
        <btn style="margin: 2%" class="btn" @click="filterEvents">Применить</btn>
        <btn style="margin: 2%" class="btn" @click="allEvents">Показать список</btn>
      </fieldset>
    </div>
  </form>
</template>

<script>
import MySelectMult from "./UI/MySelectMult.vue";
import Btn from "./UI/Btn.vue";
export default {
  components: { MySelectMult, Btn },
  emits: ["filterEvents", "allEvents"],
  data() {
    return {
      mydate: '',
      showSelect: false,
      value: null,
      options: ["Batman", "Robin", "Joker"],
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
      selectedFree: 2,
      freeOrFalse: [
        { text: "Да", value: 1 },
        { text: "Нет", value: 0 },
        { text: "Все", value: 2 },
      ],
      inputAge: 0,
    };
  },
  methods: {
    minusInput() {
      if(this.inputAge <= 18 && this.inputAge > 0)
      {
        this.inputAge -= 1;
      }
      
      
    },
    plusInput() {
      if (this.inputAge >= 0 && this.inputAge < 18)
      {
        this.inputAge += 1;
      }
      
    },
    filterEvents() {
      this.$emit(
        "filterEvents",
        this.selectCategoryEvents,
        this.selectedFree,
        this.inputAge,
        this.mydate
      );
    },
    allEvents() {
      this.$emit("allEvents");
    },
    closeOut() {
      this.showSelect = false;
    },
    closeSelectOut(arr) {
      this.selectCategoryEvents = arr;
    },
    optionMult(arr) {
      this.selectCategoryEvents = arr;
      this.showSelect = !this.showSelect;
    },
  },
};
</script>

<style>
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
.filter_developments {
  top: 200vw;
  background-color: #fff;
  z-index: 900;
  top: 0px;
  left: 300px;
  margin: 10px;
}

.number {
	display: inline-block;
	position: relative;
	width: 100px;
  margin: 2%;
}
.number input[type="number"] {
  cursor:auto;
  border: 1px solid teal;
	display: block;
	height: 32px;
	line-height: 32px;
	width: 100%;
	padding: 0;
	margin: 0;
	box-sizing: border-box;
	text-align: center;
	-moz-appearance: textfield;
	-webkit-appearance: textfield;
	appearance: textfield;
}
.number input[type="number"]::-webkit-outer-spin-button,
.number input[type="number"]::-webkit-inner-spin-button {
	display: none;
}
.number-minus {
	position: absolute;
	top: 0px;

  border-top-left-radius: 5px;
  border-bottom-left-radius: 5px;
	left: -5px;
	bottom: 0px;
	width: 30px;
	padding: 0;
	display: block;
	text-align: center;
	border: none;

	font-size: 16px;
	font-weight: 600;
  background: teal;
  cursor: pointer;
}

.number-plus {
	position: absolute;
	top: 0px;
  border-top-right-radius: 5px;
  border-bottom-right-radius: 5px;
	right: -5px;
	bottom: 0px;
	width: 30px;
	padding: 0;
	display: block;
	text-align: center;
	border: none;

	font-size: 16px;
	font-weight: 600;
  background: teal;
  cursor: pointer;
}
.date {
  border: 1px solid teal;
  margin: 5px;
}

.number-plus:active { background: rgb(22, 185, 185); } /* при нажатии */
.number-minus:active { background: rgb(22, 185, 185); } /* при нажатии */

</style>