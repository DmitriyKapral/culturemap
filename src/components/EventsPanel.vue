<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !show }">
      <button class="object__close" @click="closeAside"></button>
      <h1>Мероприятия, находящиеся в городе</h1>
      <table cellspacing="0" cellpadding="5" border="1">
        <tr>
          <th>Название мероприятия</th>
          <th>Возврастное ограничение</th>
          <th>Категория</th>
          <th>Бесплатный вход</th>
          <th></th>
        </tr>
        <template v-for="event in events">
        
        <tr v-for="selectEvent in event" :key="selectEvent._id">
          <td>{{ selectEvent.data.general.name }}</td>
          <td>{{ selectEvent.data.general.ageRestriction }}</td>
          <td>{{ selectEvent.data.general.category.name }}</td>
          <td>{{ trueFalse(selectEvent.data.general.isFree) }}</td>
          <td>
            <button
              @click="
                moveToMarker(
                  selectEvent.data.general.places[0].address.mapPosition
                    .coordinates
                )
              "
            >
              Показать
            </button>
          </td>
        </tr>
        </template>
      </table>
    </aside>
  </div>
</template>

<script>
export default {
  emits: ["moveToMarker"],
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    events: {
      type: Object,
    },
  },

  methods: {
    moveToMarker(coordinates) {
      this.$emit("moveToMarker", coordinates);
    },
    closeAside() {
      this.$emit("update:show", false);
    },
    trueFalse(Free) {
      if (!Free) {
        return "Нет";
      } else {
        return "Да";
      }
    },
  },
};
</script>

<style scoped>
.object {
  position: fixed;
  width: 40%;
  top: 0;
  left: 0%;
  bottom: 0;
  background-color: #fff;
  z-index: 1000;
  padding: 50px 30px;
  -webkit-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
  overflow-y: auto;
  text-align: left;
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
  content: "";
  display: block;
  width: 30px;
  height: 3px;
  background-color: black;
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
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>