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
            Website: {{ info.contacts.website }}
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
        <div v-if="info.selectEvents.length > 0">
          <table class="table" cellspacing="0" cellpadding="5" border="1">
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
                <btn class="submit" style="cursor: pointer;" @click="openAsideMore(event.data.general)">
                  Подробнее
                </btn>
              </td>
            </tr>
          </table>
        </div>
        <div v-else>
          <h1>Ближайшие мероприятия отсуствуют</h1>
        </div>
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
        <div class="eventtable">
          <table
            width="70%"
            cellspacing="0"
            cellpadding="10"

          >
          <tr>
            <th>Начало</th>
            <th>Конец</th>
          </tr>
            <tr v-for="seance in moreEvent.seances" v-bind:key="seance">
              <td>{{ new Date(seance.start).toLocaleString() }}</td>

              <td>{{ new Date(seance.end).toLocaleString() }}</td>
            </tr>
          </table>
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
.submit {
  display: inline-block;
  color: black;
  font-size: 125%;
  font-weight: 700;
  text-decoration: none;
  user-select: none;
  padding: .25em .5em;
  outline: none;
  border: 1px solid teal;
  border-radius: 7px;
  background: white;
  box-shadow: inset 0 -2px 1px rgba(0,0,0,0), inset 0 1px 2px rgba(0,0,0,0), inset 0 0 0 60px rgba(255,255,0,0);
  transition: box-shadow .2s, border-color .2s;
} 
.submit:hover {
  box-shadow: inset 0 -1px 1px rgba(0,0,0,0), inset 0 1px 2px rgba(0,0,0,0), inset 0 0 0 60px teal;
}
.submit:active {
  padding: calc(.25em + 1px) .5em calc(.25em - 1px);
  border-color: teal;
  box-shadow: inset 0 -1px 1px rgba(0,0,0,.1), inset 0 1px 2px rgba(0,0,0,.3), inset 0 0 0 60px rgb(24, 90, 90);
}
.eventtable {
  max-height: 500px; /* Максимальная высота */
  width: 700px;
  overflow: auto;
}
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
::-webkit-scrollbar {
	width: 6px;
} 
::-webkit-scrollbar-track {
	box-shadow: inset 0 0 6px rgba(0,0,0,0.3); 
} 
::-webkit-scrollbar-thumb {
	box-shadow: inset 0 0 6px rgba(0,0,0,0.3); 
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
  position: relative;
  left: 5%;
  top: -2%;
}
</style>