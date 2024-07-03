<template>
  <div class="spaceship" :style="styleObject"></div>
</template>

<script>
export default {
  props: {
    positionX: Number,
  },
  computed: {
    styleObject() {
      return {
        left: `${this.positionX}px`,
      };
    },
  },
};
</script>

<style scoped>
.spaceship {
  position: absolute;
  bottom: 20px;
  width: 60px;
  height: 70px;
  background-image: url("../assets/spaceship.png");
  background-size: cover;
}
</style>
