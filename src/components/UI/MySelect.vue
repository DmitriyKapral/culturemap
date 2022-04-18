<template>
  <div class="mySelect custom-dropdown">
    <p @click="areOptionsVisible = !areOptionsVisible" class="title">
      {{ selected }}
    </p>
    <div class="options" v-if="areOptionsVisible">
      <p
        v-for="option in options"
        :key="option.value"
        @click="selectOption(option)"
      >
        {{ option.name }}
      </p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
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
      areOptionsVisible: false,
    };
  },
  methods: {
    selectOption(option) {
      this.$emit("select", option);
      this.areOptionsVisible = false;
    },
    hideSelect() {
        this.areOptionsVisible = false;
    }
  },
  mounted() {
      document.addEventListener('click', this.hideSelect, true)
  },
  beforeUnmount() {
      document.removeEventListener('click', this.hideSelect, true)
  },
};
</script>

<style scoped>
.mySelect {
  cursor: pointer;
  position: relative;
  width: 200px;
}
p {
  margin: 0;
}
.options {
  border: solid 1px gray;
  position: absolute;
  background: white;
  top: 30px;
  right: 0;
  width: 100%;

}
.title {
  border: solid 1px;
}
.options p:hover {
  background: gray;
}


/* Custom dropdown */
.custom-dropdown {
  position: relative;
  display: inline-block;
  vertical-align: middle;
  margin: 5px; /* demo only */
}

.custom-dropdown select {

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
  outline:none;
}

.custom-dropdown::before,
.custom-dropdown::after {
  content: "";
  position: absolute;
  pointer-events: none;
}

.custom-dropdown::after {
  /*  Custom dropdown arrow */
  content: "\25BC";
  height: 1em;
  font-size: 0.625em;
  line-height: 1;
  right: 1.2em;
  top: 50%;
  margin-top: -0.5em;
}

.custom-dropdown::before {
  /*  Custom dropdown arrow cover */
  width: 2em;
  right: 0;
  top: 0;
  bottom: 0;
  border-radius: 0 3px 3px 0;
}

.custom-dropdown select[disabled] {
  color: teal;
}

.custom-dropdown select[disabled]::after {
  color: teal;
}

.custom-dropdown::before {
  background-color: teal;
}

.custom-dropdown::after {
  color: rgba(0, 0, 0, 0.4);
}









</style>