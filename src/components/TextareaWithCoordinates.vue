<template>
  <div class="container">
    <textarea
            class="pad"
            :rows="rows"
            @input="updateCoordinates"
            @keydown="updateCoordinates"
            @click="updateCoordinates"
            @scroll="updateCoordinates"
            v-model="value">
    </textarea>
    <slot :coordinates="coordinates" :value="value"></slot>
  </div>
</template>

<script>
import {getCaretCoordinates} from "../getCaretCoordinates";

export default {
  name: 'TextareaWithCoordinates',
  data() {
    return {
      value: "",
      coordinates: {
        height: 0,
        left: 0,
        top: 0
      }
    }
  },
  beforeMount() {
    this.value = this.initialValue;
  },
  methods: {
    updateCoordinates(e) {
      this.coordinates = getCaretCoordinates(e.target, e.target.selectionEnd);
      this.emit("coordinatesChanged", this.coordinates);
      this.emit("change", this.value);
    }
  },
  props: {
    initialValue: {
      type: String,
      required: true
    },
    rows: {
      type: Number,
      default: 10
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container, .pad {
    margin: 0;
    padding: 0;
  }
  .container {
    position: relative;
  }
  .pad {
    width: 100%;
    resize: none
  }
</style>
