<template>
  <div class="layout-container">
    <Game />
  </div>
</template>

<script>
import Game from "../components/Game.vue";

export default {
  components: {
    Game,
  },
};
</script>

<style scoped>
.layout-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  background-color: #282c34;
  color: white;
  font-size: calc(10px + 2vmin);
}
</style>
