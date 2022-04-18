<template>
  <div>
    <aside class="object" v-bind:class="{ object_closed: !info.flag }">
      <div class="pos">
        <button
          v-for="tab in tabs"
          v-bind:key="tab"
          v-bind:class="['tab-button', { active: currentTab === tab }]"
          v-on:click="currentTab = tab"
        >
          {{ tab }}
        </button>
        <component v-bind:is="currentTabComponent" class="tab"></component>
      </div>

      <button class="object__close" @click="closeAside"></button>

      <div v-if="bool">
        <h2>{{ info.name }}</h2>
        <div v-html="info.description"></div>
        <div class="object__img">
          <img :src="info.img" alt="" class="img-absolute" />
        </div>
        <div>
          <p>Контакты для связи:</p>
          <div v-if="info.contacts?.hasOwnProperty('email')">
            <p>Email: {{ info.contacts.email }}</p>
          </div>

          <div v-if="info.contacts?.hasOwnProperty('phones')">
            Телефоны:
            <div v-for="phone in info.contacts.phones" :key="phone">
              +{{ phone.value }}
            </div>
          </div>
          <div v-if="info.contacts?.hasOwnProperty('website')">
            Website: {{info.contacts.website}}
          </div>
          <strong>Дни работы:</strong>
          <div v-for="(work, index) in info.workingSchedule" v-bind:key="work">
            {{ daysOfWeek[index] }}: {{ work.from }} - {{ work.to }}
          </div>
          
        </div>
        <div class="object__descr"></div>
      </div>
      <div v-if="!bool">
        <h1>{{ info.name }}</h1>
        <table cellspacing="0" cellpadding="5" border="1">
          <tr>
            <th>Название мероприятия</th>
            <th>Возврастное ограничение</th>
            <th>Категория</th>
            <th>Бесплатный вход</th>
            <th></th>
          </tr>
          <tr v-for="event in info.selectEvents" v-bind:key="event._id">
            <td>{{ event.data.general.name }}</td>
            <td>{{ event.data.general.ageRestriction }}</td>
            <td>{{ event.data.general.category.name }}</td>
            <td>{{ trueFalse(event.data.general.isFree) }}</td>
            <td>
              <button @click="openAsideMore(event.data.general)">
                Подробнее
              </button>
            </td>
          </tr>
        </table>
      </div>
    </aside>
    <div>
      <aside class="more object" v-bind:class="{ object_closed: !more }">
        <button class="object__close" @click="closeAsideMore"></button>
        <h2>{{ moreEvent.name }}</h2>
        <div v-html="moreEvent.description"></div>
        <div v-if="moreEvent.hasOwnProperty('image')" class="object__img">
          <img :src="moreEvent.image.url" alt="" class="img-absolute" />
        </div>
        <div v-if="moreEvent.price">
          <strong>Стоимость входа: </strong>{{ moreEvent.price }}
        </div>
        <div>Сеансы:</div>
        <div v-for="seance in moreEvent.seances" v-bind:key="seance">
          {{ new Date(seance.start).toLocaleString() }} -
          {{ new Date(seance.end).toLocaleString() }}
        </div>
      </aside>
    </div>
  </div>
</template>

<script>
export default {
  emits: ["closeAside"],
  data() {
    return {
      flagClose: false,
      daysOfWeek: [
        "Понедельник",
        "Вторник",
        "Среда",
        "Четверг",
        "Пятница",
        "Суббота",
        "Воскресенье",
      ],
      currentTab: "Информация",
      tabs: ["Информация", "Мероприятия"],
      bool: true,
      events: [],
      moreEvent: [],
      isFree: "",
      more: false,
    };
  },
  props: {
    info: {
      type: Object,
    },
  },
  methods: {
    closeAside() {
      this.flagClose = false;
      this.$emit("closeAside", this.flagClose);
    },
    closeAsideMore() {
      this.more = false;
    },
    openAsideMore(moreData) {
      this.more = true;
      this.moreEvent = moreData;
    },
    currentTabComponent() {
      if (this.currentTab == "Мероприятия") {
        this.bool = false;
      } else {
        this.bool = true;
      }
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
.img-absolute {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  -o-object-fit: cover;
  object-fit: cover;
}
.more {
  z-index: 1001;
}
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

.pos {
  position: absolute;
  left: 10%;
  top: 3%;
}
</style>