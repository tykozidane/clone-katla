<template>
    <div v-if="visible" class="popup">
      <p>{{ message }}</p>
      <button @click="close" v-if="showCloseButton" class="close-btn">✖</button>
    </div>
  </template>
  
  <script>
  export default {
    props: ['message', 'showCloseButton'],
    data() {
      return {
        visible: false
      };
    },
    methods: {
      show() {
        this.visible = true;
        if (!this.showCloseButton) {
          setTimeout(() => {
            this.visible = false;
          }, 3000); // Display for 3 seconds
        }
      },
      close() {
        this.visible = false;
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
    text-align: center;
  }
  .close-btn {
    background: none;
    border: none;
    color: white;
    font-size: 1.2rem;
    cursor: pointer;
    position: absolute;
    top: 5px;
    right: 10px;
  }
  </style>
  