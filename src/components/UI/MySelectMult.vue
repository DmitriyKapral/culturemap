<template>
  <div class="mySelect custom-dropdowns">
    <p @click.stop="hideSelect" class="title">
      Выбрано: <strong>{{ checkedNames.length }}</strong>
    </p>
    <div @click.stop class="options" v-if="show">
      <div v-for="option in options" :key="option.value">
        <input
          style="float: left"
          type="checkbox"
          class="custom-checkbox"
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
    options: {
      type: Array,
      default() {
        return [];
      },
    },
    selectedOptions: {
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
      if (this.show) {
        this.$emit("all", this.checkedNames);
      }
    },
  },
  mounted() {
    this.checkedNames = this.selectedOptions;
    document.addEventListener("click", this.hideSelects.bind(this), true);
    //document.addEventListener('click', this.clickOut.bind(this), true)
  },
  beforeUnmount() {
    // document.removeEventListener('click', this.hideSelects)
    document.removeEventListener("click", this.hideSelects);
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
  top: 42px;
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
.custom-checkbox {
  position: absolute;
  z-index: -1;
  opacity: 0;
}
.custom-checkbox+label {
  display: inline-flex;
  align-items: center;
  user-select: none;
}
.custom-checkbox+label::before {
  content: '';
  display: inline-block;
  width: 1em;
  height: 1em;
  flex-shrink: 0;
  flex-grow: 0;
  border: 1px solid #adb5bd;
  border-radius: 0.25em;
  margin-right: 0.5em;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: 50% 50%;
}
.custom-checkbox:checked+label::before {
  border-color: teal;
  background-color: teal;
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 8 8'%3e%3cpath fill='%23fff' d='M6.564.75l-3.59 3.612-1.538-1.55L0 4.26 2.974 7.25 8 2.193z'/%3e%3c/svg%3e");
}
/* стили при наведении курсора на checkbox */
.custom-checkbox:not(:disabled):not(:checked)+label:hover::before {
  border-color: #b3d7ff;
}
/* стили для активного состояния чекбокса (при нажатии на него) */
.custom-checkbox:not(:disabled):active+label::before {
  background-color: teal;
  border-color: teal;
}
/* стили для чекбокса, находящегося в фокусе */
.custom-checkbox:focus+label::before {
  box-shadow: 0 0 0 0.2rem rgba(3, 194, 130, 0.25);
}
/* стили для чекбокса, находящегося в фокусе и не находящегося в состоянии checked */
.custom-checkbox:focus:not(:checked)+label::before {
  border-color: #80bdff;
}
/* стили для чекбокса, находящегося в состоянии disabled */
.custom-checkbox:disabled+label::before {
  background-color: #e9ecef;
}
</style>