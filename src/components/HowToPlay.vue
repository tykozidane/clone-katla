<template>
    <div v-if="visible" class=" w-full h-full absolute z-50">
        <div class="w-full h-full bg-slate-900 opacity-25 relative">
            <div class="w-5/6 mx-auto absolute popup">
            <button @click="close" class="close-btn">✖</button>
            <h2 id="dialogTitle" class="text-center uppercase font-semibold my-4">Cara Bermain</h2>
        </div>
        </div>
        
      
    </div>
  </template>
  
  <script>
  export default {
    name:"HowToPlay",
    // props: [],
    data() {
      return {
        visible: false
      };
    },
    methods: {
      show() {
        this.visible = true;
      },
      close() {
        this.visible = false;
      }
    }
  };
  </script>
  
  <style scoped>
  .popup {
    /* position: absolute;
    top: 20%;
    left: 50%; */
    /* transform: translateX(-50%); */
    background-color: #111827;
    color: white;
    /* padding: 10px 20px;
    /* border-radius: 5px; */
    z-index: 1000;
    /* text-align: center; */
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
  