<template>
  <form class="filter">
    <div>
      <fieldset>
        <legend>Фильтры объектов</legend>
        <select

          v-model="selectRadius"
        >
          <option value="500">500 метров</option>
          <option value="1000">1000 метров</option>
          <option value="1500">1500 метров</option>
          <option value="3000">3000 метров</option>
          <option value="100000">Весь город</option>
        </select>
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
        <button @click="filterObject" type="button" class="btn btn-outline-dark">
          Применить
        </button>
      </fieldset>
    </div>
  </form>
</template>

<script>
export default {
  emits: ["filterObject"],
  data() {
    return {
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
    }
  },
  methods: {
    filterObject() {
      this.$emit('filterObject', this.selectCategory, this.selectRadius);
    }
  }
};
</script>

<style scoped>
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