<template>
  <div id="app">
    <TextareaWithCoordinates :key="value" :initial-value="value">
      <template v-slot:default="{ coordinates, value }">
        <span class="tracker" :style="{ left: coordinates.left + 'px', top: coordinates.top + 'px' }">
          {{ JSON.stringify(coordinates) }} {{ JSON.stringify(value) }}
        </span>
      </template>
    </TextareaWithCoordinates>
  </div>
</template>

<script>
import TextareaWithCoordinates from "./components/TextareaWithCoordinates";

export default {
  name: 'App',
  components: {
    TextareaWithCoordinates,
  },
  data() {
    return {
      value: "dddd"
    }
  }
}
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin-top: 60px;
  }
  .tracker {
    position: absolute;
    width: 10px;
    height: 10px;
    display: block;
    background: red;
  }
</style>
