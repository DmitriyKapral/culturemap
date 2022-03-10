<template>
   <div id="parentx">

    <vs-button @click="active=!active" color="primary" type="filled">Open Sidebar</vs-button>
    <vs-sidebar parent="body" default-index="1"  color="primary" class="sidebarx" spacer v-model="active">

      <div class="header-sidebar">
        <vs-avatar  size="70px" src="https://randomuser.me/api/portraits/men/85.jpg"/>

        <h4>
          My Name
          <vs-button color="primary" icon="more_horiz" type="flat"></vs-button>
        </h4>

      </div>

      <vs-sidebar-item index="1" icon="question_answer">
        Dashboard
      </vs-sidebar-item>

      <vs-sidebar-item index="2" icon="gavel">
        History
      </vs-sidebar-item>

      <vs-divider icon="person" position="left">
        User
      </vs-divider>

      <vs-sidebar-item index="3" icon="verified_user">
        Configurations
      </vs-sidebar-item>
      <vs-sidebar-item index="4" icon="account_box">
        Profile
      </vs-sidebar-item>
      <vs-sidebar-item index="5" >
        Card
      </vs-sidebar-item>

      <div class="footer-sidebar">
        <vs-button icon="reply" color="danger" type="flat">log out</vs-button>
        <vs-button icon="settings" color="primary" type="border"></vs-button>
      </div>

    </vs-sidebar>
  </div>


:icon="icon"



<div id="map">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <get-markers :data="dataLibrary" :icon="iconLibrary" @getInfo="getData" />
      <get-markers :data="dataCinema" :icon="iconCinema" @getInfo="getData" />
      <get-markers
        :data="dataCircuses"
        :icon="iconCircuses"
        @getInfo="getData"
      />
      <get-markers :data="dataConcert" :icon="iconConcert" @getInfo="getData" />
      <get-markers :data="dataMuseums" :icon="iconMuseums" @getInfo="getData" />
      <get-markers :data="dataParks" :icon="iconParks" @getInfo="getData" />
      <get-markers
        :data="dataTheaters"
        :icon="iconTheaters"
        @getInfo="getData"
      />
    </l-map>
    <aside class="object" v-bind:class="{ object_closed: !flag }">
      <button class="object__close" @click="closeAside"></button>
      <button @click="locationButtonPressed">Нажми на меня</button>
      <div class="object__title"></div>
      <h2>{{ name }}</h2>
      <div v-html="description"></div>
      <div class="object__img">
        <img :src="img" alt="" class="img-absolute" />
      </div>
      <div class="object__descr"></div>
    </aside>
    
  </div>







</template>








<script>
export default {
  data:()=>({
    active:false,
  })
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus">
.header-sidebar
  display flex
  align-items center
  justify-content center
  flex-direction column
  width 100%
  h4
    display flex
    align-items center
    justify-content center
    width 100%
    > button
      margin-left 10px
.footer-sidebar
  display flex
  align-items center
  justify-content space-between
  width 100%
  > button
      border 0px solid rgba(0,0,0,0) !important
      border-left 1px solid rgba(0,0,0,.07) !important
      border-radius 0px !important
</style>