<template>
  <LayoutContainer />
</template>

<script>
import LayoutContainer from "./views/MainLayout.vue";

export default {
  name: "App",
  components: {
    LayoutContainer,
  },
};
</script>

<style>
#app {
  font-family: "IBM Plex Sans", Inter, Helvetica, Arial, sans-serif;
  text-align: center;
}
</style>
