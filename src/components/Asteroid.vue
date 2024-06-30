<template>
  <div class="asteroid" :style="styleObject"></div>
</template>

<script>
export default {
  props: {
    positionX: Number,
    positionY: Number,
  },
  computed: {
    styleObject() {
      return {
        left: `${this.positionX}px`,
        top: `${this.positionY}px`,
      };
    },
  },
};
</script>

<style scoped>
.asteroid {
  position: absolute;
  width: 50px;
  height: 50px;
  background-image: url('../assets/asteroid.png');
  background-size: cover;
}
</style>
