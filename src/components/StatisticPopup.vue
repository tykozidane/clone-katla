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
          <div class="grid grid-cols-4 justify-items-center content-start w-10/12 mx-auto">
            <div class="flex flex-col justify-start items-center w-fit">
              <div class=" text-3xl">
                {{ totalPlaying }}
              </div>
              <div class=" text-sm">Dimainkan</div>
            </div>
            <div class="flex flex-col justify-start items-center w-fit">
              <div class=" text-3xl">
                {{ persenWin }}
              </div>
              <div class="text-sm">% Menang</div>
            </div>
            <div class="flex flex-col justify-start items-center w-fit">
              <div class=" text-3xl">
                {{ winStreakNow }}
              </div>
              <div class="text-sm">Runtutan<br> saat ini</div>
            </div>
            <div class="flex flex-col justify-start items-center w-fit">
              <div class=" text-3xl">
                {{ winStreak }}
              </div>
              <div class="text-sm">Runtutan<br> maksimum</div>
            </div>
          </div>
          <div class=" w-10/12 mx-auto flex flex-col mt-10 font-semibold">
            <div>DISTRIBUSI TEBAKAN</div>
            <div class="flex flex-col mt-5 w-full">
               <div class="flex">
                  <div>1</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[0], ({'items-end' : center[0]})" > {{ gameStats.distribution[1] }} </div>
               </div>
               <div class="flex">
                  <div>2</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[1], ({'items-end' : center[1]})" > {{ gameStats.distribution[2] }} </div>
               </div>
               <div class="flex">
                  <div>3</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[2], ({'items-end' : center[2]})" > {{ gameStats.distribution[3] }} </div>
               </div>
               <div class="flex">
                  <div>4</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[3], ({'items-end' : center[3]})" > {{ gameStats.distribution[4] }} </div>
               </div>
               <div class="flex">
                  <div>5</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[4], ({'items-end' : center[4]})" > {{ gameStats.distribution[5] }} </div>
               </div>
               <div class="flex">
                  <div>6</div>
                  <div class="ml-1 bg-slate-400 flex justify-center items-center" :class="width[5], ({'items-end' : center[5]})" > {{ gameStats.distribution[6] }} </div>
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
    props: ["gameStats", "totalPlaying", "persenWin", "winStreak", "winStreakNow", "highestValue"],
    data() {
      return {
        visible: false,
        number: 'w-['+70+'%]',
        width: [],
        center: []
      };
    },
    mounted() {
      for(let i=0;i<6;i++){
        if(this.gameStats.distribution[i+1] > 0){
          this.width[i]= 'w-['+(this.gameStats.distribution[i+1] / this.highestValue * 100)+'%]'
          this.center[i] = false
        } else {
          this.width[i] = 'w-[10%]'
          this.center[i] = true
        console.log("width " + i, this.gameStats.distribution[i+1])

        }
        // if((this.gameStats.distribution[i+1] / this.highestValue * 100) < 10) {
        //   this.width[i] = 'w-[10%]'
        // }
        console.log("width " + i, this.width[i])
      }
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
  