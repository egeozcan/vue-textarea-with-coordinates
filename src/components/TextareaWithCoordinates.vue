<template>
  <div class="container">
    <textarea
      ref="pad"
      class="pad"
      :rows="rows"
      @input="updateCoordinates"
      @keydown="updateCoordinates"
      @click="updateCoordinates"
      @scroll="updateCoordinates"
      @focus="setFocusState(true)"
      @blur="setFocusState(false)"
      v-model="value"
    >
    </textarea>
    <slot
      :coordinates="coordinates"
      :value="value"
      :size-data="sizeData"
      :has-focus="hasFocus"
      :selection="selection"
    ></slot>
  </div>
</template>

<script>
import { getCaretCoordinates } from "../getCaretCoordinates";

export default {
  name: "TextareaWithCoordinates",
  data() {
    return {
      value: "",
      selection: {
        start: 0,
        end: 0
      },
      coordinates: {
        height: 0,
        left: 0,
        top: 0,
      },
      sizeData: {
        borderbox: {
          inlineSize: 0,
          blockSize: 0,
        },
        contentBox: {
          inlineSize: 0,
          blockSize: 0,
        },
        borderBox: {
          inlineSize: 0,
          blockSize: 0,
        },
      },
      lastValue: null,
      resizeObserver: null,
      hasFocus: false,
    };
  },
  beforeMount() {
    this.value = this.initialValue;
  },
  mounted() {
    if (!window.ResizeObserver) {
      return;
    }

    new ResizeObserver((sizeDataArray) => {
      const sizeData = sizeDataArray[0];
      this.sizeData = {
        borderBox: {
          inlineSize: sizeData.borderBoxSize.inlineSize,
          blockSize: sizeData.borderBoxSize.blockSize,
        },
        contentBox: {
          inlineSize: sizeData.contentBoxSize.inlineSize,
          blockSize: sizeData.contentBoxSize.blockSize,
        },
        contentRect: sizeData.contentRect,
      };
      this.$emit("sizeData", this.sizeData);
      this.updateCoordinates();
    }).observe(this.$refs.pad);
  },
  beforeDestroy() {
    if (!this.resizeObserver) {
      return;
    }

    this.resizeObserver.unobserve(this.$refs.pad);
  },
  methods: {
    updateCoordinates(e = {}) {
      const target = e.target ?? this.$refs.pad;
      this.selection.start = target.selectionStart;
      this.selection.end = target.selectionEnd;
      this.coordinates = getCaretCoordinates(target, target.selectionEnd);
      this.$emit("coordinatesChanged", this.coordinates);
      this.$emit("selection", this.selection);

      if (this.lastValue === this.value) {
        return;
      }

      this.lastValue = this.value;
      this.$emit("change", this.value);
    },
    setFocusState(val) {
      this.hasFocus = val;
      this.$emit("focusChange", this.hasFocus);
    },
  },
  props: {
    initialValue: {
      type: String,
      required: true,
    },
    rows: {
      type: Number,
      default: 10,
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container,
.pad {
  margin: 0;
  padding: 0;
}
.container {
  position: relative;
}
.pad {
  width: 100%;
  resize: none;
}
</style>
