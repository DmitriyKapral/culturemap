<template>
  <div class="custom-select" :tabindex="tabindex" @blur="open = false">
    <div class="selected" :class="{ open: open }" @click="open = !open">
      {{ selected }}
    </div>
    <div class="items" :class="{ selectHide: !open }">
      <div
        v-for="(option, i) of options"
        :key="i"
        @click="
          selected = option.name;
          open = false;
          $emit('input', option.value);
        "
      >
        {{ option.name }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    options: {
      type: Array,
      required: true,
    },
    default: {
      type: String,
      required: false,
      default: null,
    },
    tabindex: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  data() {
    return {
      selected: this.default
        ? this.default
        : this.options.length > 0
        ? this.options[0]
        : null,
      open: false,
    };
  },

};
</script>

<style scoped>
/* Custom dropdown */

.custom-select {
  position: relative;
  width: 200px;
  text-align: left;
  outline: none;
  height: 47px;
  left: 31%;
  line-height: 47px;
  
  
}

.custom-select::before,
.custom-select::after {
  content: "";
  position: absolute;
  pointer-events: none;
}

.custom-select::after {
  /*  Custom dropdown arrow */
  content: "\25BC";
  height: 1em;
  font-size: 0.625em;
  line-height: 1;
  right: 1.2em;
  top: 50%;
  margin-top: -0.5em;
  
}

.custom-select::before {
  /*  Custom dropdown arrow cover */
  width: 2em;
  right: 0;
  top: 0;
  height: 104%;
  bottom: 0;
  border-radius: 0 6px 6px 0;
  
}

.custom-select::before {
  background-color: teal;
  
}


.custom-select::after {
  color: rgba(0, 0, 0, 0.4);
  
}



.custom-select .selected {
  background-color: white;
  border-radius: 6px;
  border: 1px solid teal;
  color: black;
  padding-left: 1em;
  cursor: pointer;
  user-select: none;
  
}

.custom-select .selected.open {
  border: 1px solid teal;
  border-radius: 6px 6px 0px 0px;
}

.custom-select .selected:after {
  position: absolute;
  content: "";
  top: 22px;
  right: 1em;
  width: 0;
  height: 0;
  border: 5px solid transparent;

  
}

.custom-select .items {
  color: black;
  border-radius: 0px 0px 6px 6px;
  overflow: hidden;
  border-right: 1px solid teal;
  border-left: 1px solid teal;
  border-bottom: 1px solid teal;
  position: absolute;
  background-color: white;
  left: 0;
  right: 0;
  z-index: 1;
}

.custom-select .items div {
  color: black;
  padding-left: 1em;
  cursor: pointer;
  user-select: none;
}

.custom-select .items div:hover {
  background-color: teal;
}

.selectHide {
  display: none;
}
</style>