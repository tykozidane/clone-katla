<template>
    <div v-if="visible" class="popup">
      <p>{{ message }}</p>
    </div>
  </template>
  
  <script>
  export default {
    props: ['message'],
    data() {
      return {
        visible: false
      };
    },
    methods: {
      show() {
        this.visible = true;
        setTimeout(() => {
          this.visible = false;
        }, 3000); // Display for 3 seconds
      }
    }
  };
  </script>
  
  <style scoped>
  .popup {
    position: fixed;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    background-color: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 10px 20px;
    border-radius: 5px;
    z-index: 1000;
  }
  </style>
  