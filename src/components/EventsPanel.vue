<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !show }">
      <button class="object__close" @click="closeAside"></button>

      <h1>Мероприятия, находящиеся в городе</h1>
      <table class="table" cellspacing="0" cellpadding="5" border="1">
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
              <btn
                @click="
                  moveToMarker(
                    selectEvent.data.general.places[0].address.mapPosition
                      .coordinates
                  )
                "
              >
                Показать
              </btn>
              
            </td>
          </tr>
        </template>
      </table>
    </aside>
  </div>
</template>

<script>
import Btn from './UI/Btn.vue';
export default {
  components: { Btn },
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
* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

 
/* Стили для скролла */
::-webkit-scrollbar {
	width: 6px;
} 
::-webkit-scrollbar-track {
	box-shadow: inset 0 0 6px rgba(0,0,0,0.3); 
} 
::-webkit-scrollbar-thumb {
	box-shadow: inset 0 0 6px rgba(0,0,0,0.3); 
}
</style>