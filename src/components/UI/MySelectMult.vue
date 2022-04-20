<template>
  <div class="mySelect custom-dropdowns">
    <p @click.stop="hideSelect" class="title">
      Выбрано: <strong>{{ checkedNames.length }}</strong>
    </p>
    <div @click.stop class="options" v-if="show">
      <div v-for="option in options" :key="option.value">
        <input
          
          type="checkbox"
          :value="option.value"
          v-model="checkedNames"
          :id="option.value"
          
        />
        <label :for="option.value">{{ option.name }}</label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    selected: {
      type: String,
    },
    options: {
      type: Array,
      default() {
        return [];
      },
    },
  },
  data() {
    return {
      checkedNames: [],
      areOptionsVisible: false,
    };
  },
  methods: {
    hideSelect() {
      this.$emit("select", this.checkedNames);
      //this.areOptionsVisible = !this.areOptionsVisible;
    },
    hideSelects() {
      if(this.show)
      {
        this.$emit("all", this.checkedNames);
      }
    },
  },
  mounted() {
    document.addEventListener('click', this.hideSelects.bind(this), true);
    //document.addEventListener('click', this.clickOut.bind(this), true)
  },
  beforeUnmount() {
      // document.removeEventListener('click', this.hideSelects)
      document.removeEventListener('click', this.hideSelects)
  },
};
</script>

<style scoped>
.mySelect {
  cursor: pointer;
  position: relative;
  width: 200px;
}
.options {
  border: solid 1px gray;
  position: absolute;
  background: white;
  top: 35px;
  right: 0;
  width: 100%;
  z-index: 99;
}
.title {
  border: solid 1px;
}
.options p:hover {
  background: gray;
}

/* Custom dropdown */
.custom-dropdowns {
  position: relative;
  display: inline-block;
  vertical-align: middle;
  margin: 5px; /* demo only */
}

.custom-dropdowns p {
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
  outline: none;
}

.custom-dropdowns::before,
.custom-dropdowns::after {
  content: "";
  position: absolute;
  pointer-events: none;
}

.custom-dropdowns::after {
  /*  Custom dropdown arrow */
  content: "\25BC";
  height: 1em;
  font-size: 0.625em;
  line-height: 1;
  right: 1.2em;
  top: 50%;
  margin-top: -0.5em;
}

.custom-dropdowns::before {
  /*  Custom dropdown arrow cover */
  width: 2em;
  right: 0;
  top: 0;
  bottom: 0;
  border-radius: 0 3px 3px 0;
}

.custom-dropdowns::before {
  background-color: teal;
}

.custom-dropdowns::after {
  color: rgba(0, 0, 0, 0.4);
}
</style>