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

* {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
</style>