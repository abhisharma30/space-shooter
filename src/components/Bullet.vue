<template>
    <div class="bullet" :style="styleObject"></div>
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
  .bullet {
    position: absolute;
    width: 10px;
    height: 10px;
    background-color: #ff0040;
    border-radius: 5px;
  }
  </style>
  