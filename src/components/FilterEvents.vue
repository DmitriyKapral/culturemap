<template>
  <form class="filter_developments">
    <div>
      <fieldset>
        <legend>Фильтры объектов</legend>
        <select
          class="form-select"
          multiple
          aria-label="multiple select example"
          v-model="selectCategoryEvents"
        >
          <option v-for="category in categoryEvents" v-bind:key="category">
            {{ category }}
          </option>
        </select>
        <br />
        <select v-model="selectedFree">
          <option
            v-for="FreeFalse in freeOrFalse"
            v-bind:key="FreeFalse"
            :value="FreeFalse.value"
          >
            {{ FreeFalse.text }}
          </option>
        </select>
        <br />
        <input v-model="inputAge" type="number" min="0" max="18" />
        <br />
        <button
          type="button"
          class="btn btn-outline-dark"
          @click="filterEvents"
        >
          Применить
        </button>
        <button type="button" class="btn btn-outline-dark">
          Показать список и применить
        </button>
      </fieldset>
    </div>
  </form>
</template>

<script>
export default {
  emits: ["filterEvents"],
  data() {
    return {
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
      filterEvents() {
          this.$emit('filterEvents', this.selectCategoryEvents, this.selectedFree, this.inputAge);
      }
  }
};
</script>

<style scoped>
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
.filter_developments {
  position: fixed;
  top: 200vw;
  background-color: #fff;
  z-index: 900;
  top: 0px;
  left: 300px;
  margin: 10px;
}
</style>