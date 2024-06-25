<template> 
    <div v-if="visible" class=" w-full h-full absolute z-50 bg-black bg-opacity-60" >
      <div class="w-full h-full relative flex justify-center">
        <div class="absolute w-full h-full"  @click="close">
        </div>
        <div class="max-w-lg popup h-fit w-full relative mt-16 pt-4 px-4 flex flex-col">
            <div class="w-full mx-auto flex justify-center ">
            <button @click="close" class="close-btn-htp">✖</button>
            <h2 id="dialogTitle" class="text-center uppercase font-semibold my-4">STATISTIK</h2>
          </div>
          <div class="flex justify-center items-center mx-auto">
            <div class="flex flex-col">
              <div>
                {{ totalPlaying }}
              </div>
            </div>
          </div>
        </div>
      </div>
        
    </div>
  </template>
  
  <script>
  export default {
    name:"StatisticPopup",
    props: ["gameStats", "totalPlaying"],
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
   .br { 
            display: block; 
            margin-bottom: 0.6em; 
        } 
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
  .close-btn-htp {
    background: none;
    border: none;
    color: white;
    font-size: 0.9rem;
    font-weight: 100;
    /* cursor: pointer; */
    position: absolute;
    top: 15px;
    right: 20px;
  }
  .line {
  /* border-color: #6b7280; */
  border-top: 1px solid #6b7280;
  /* border-top: 1px solid white; */
}
  .front {
    width: 47px;
    height: 43px;
    font-size: 2.2rem;
    font-weight: 700;
    border-radius: 3px;
    border: 3px solid #6b7280 ;
  }
  .front-correct {
    width: 47px;
    height: 44px;
    font-size: 2.2rem;
    font-weight: 700;
    border-radius: 3px;
    animation: flipcorrect 1s forwards;
    animation-duration: 2s;
    transform: rotateX(0deg);
    border: none;
    background-color: #111827;

    /* border: 3px solid #6b7280 ; */
  }
  @keyframes flipcorrect {
  0% { transform: rotateX(180deg); background-color: #111827;  }
  100% { transform: rotateX(360deg); background-color: #15803d;  }
  }
  .front-present {
    width: 47px;
    height: 44px;
    font-size: 2.2rem;
    font-weight: 700;
    border-radius: 3px;
    animation: flippresent 1s forwards;
    animation-duration: 2s;
    transform: rotateX(0deg);
    border: none;
    background-color: #111827;

    /* border: 3px solid #6b7280 ; */
  }
  @keyframes flippresent {
  0% { transform: rotateX(180deg); background-color: #111827;  }
  100% { transform: rotateX(360deg); background-color: #ca8a04;  }
  }
  .front-absent {
    width: 47px;
    height: 44px;
    font-size: 2.2rem;
    font-weight: 700;
    border-radius: 3px;
    animation: flipabsent 1s forwards;
    animation-duration: 2s;
    transform: rotateX(0deg);
    border: none;
    background-color: #111827;

    /* border: 3px solid #6b7280 ; */
  }
  @keyframes flipabsent {
  0% { transform: rotateX(180deg); background-color: #111827;  }
  100% { transform: rotateX(360deg); background-color: #374151;  }
  }
  </style>
  