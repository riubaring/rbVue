<template>
  <div class="overlay" :style="setOverlayStyle">
    <div class="overlay-spacing">
      <rise-loader v-bind:loading="isProcessing" size="35px" color="#32C5D2"></rise-loader>
    </div>
  </div>
</template>
<script>
export default {
  computed: {
    setOverlayStyle() {
      if (this.isProcessing) {
        return "diplay: block;";
      } else {
        return "display: none;";
      }
    }
  },
  props: ["isProcessing"],
  data() {
    return {};
  }
};
</script>
<style lang="css" scoped>
.overlay {
  position: absolute;
  height: 100%;
  top: 0;
  left: 0;
  right: 0;
  z-index: 9;
  background: rgba(238, 241, 245, 0.5);
}
.overlay-spacing {
  position: fixed;
  top: 50%;
  left: 50%;
  z-index: 1771;
}
.v-rise {
  border-radius: 100% !important;
}
</style>