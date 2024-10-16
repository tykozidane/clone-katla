<template>
  <div class="flex flex-col base justify-start items-center h-screen bg-gray-900 text-white px-3 md:pb-5 lg:px-0 lg:pb-0 relative overflow-hidden overflow-y-hidden">  
    <HowToPlay ref="howtoplay" />
    <StatisticPopup ref="statisticPopup" :gameStats="gameStats" :finish="finish" :highestValue="highestValue" :totalPlaying="totalPlaying" :persenWin="persenWin" :winStreak="winStreak" :winStreakNow="winStreakNow" />
    <HeaderComp @showHowToPlay="showHowToPlay" @showStatistic="showStatistic" :numberOfDays="numberOfDays"/>
    <PopupMessage ref="popup" message="Tidak ada di KBBI" />
    <PopupMessage ref="finishedPopup" :message="greetingMessage[numberGreeting]" :showCloseButton="false" />
    <div class="outerbox mx-auto grow-0 shrink grid grid-rows-6 gap-[6px] w-full max-h-[400px] h-full md:h-4/5 md:max-h-full mt-3 my-2 px-2 mini:px-8" >    
      <div v-for="(row, rowIndex) in rows" :key="rowIndex" class="grid grid-cols-5 gap-[6px]">
      <div v-for="(box, boxIndex) in row.boxes" :key="boxIndex" class="w-full h-full">
        <div
          class="box w-full h-full font-bold mini:text-4xl text-3xl"
          :class="{ flip: row.locked, shake: row.shake }"
          :style="{ animationDelay: row.locked ? `${boxIndex * 0.5}s` : '0s' }"
        >
          <div class="front w-full h-full" :class="{ animate: box.active }">{{ box.letter }}</div>
          <div class="back w-full h-full" :class="box.color">{{ box.letter }}</div>
        </div>
      </div>
    </div>
    </div>
    
    <KeyboardButtons @key-press="handleKeydown" ref="keyboard" />
    <div class="mt-4 text-xs mb-5">
      Clone from <a class="font-bold">katla.id</a> by <a class="font-bold">Tyko Zidane Badhawi</a>
    </div>
  </div>
</template>

<script>
import { words } from '../assets/data.json';
import PopupMessage from './PopupMessage.vue';
import KeyboardButtons from './KeyboardButtons.vue';
import HeaderComp from './HeaderComp.vue';
import HowToPlay from './HowToPlay.vue';
import StatisticPopup from './StatisticPopup.vue';
import axios from 'axios'

export default {
  components: {
    PopupMessage,
    KeyboardButtons,
    HeaderComp,
    HowToPlay,
    StatisticPopup
  },
  data() {
    return {
      wordOfTheDay: '',
      numberOfDays: 0,
      invalidWords: [],
      rows: Array(6).fill().map(() => ({
        boxes: Array(5).fill().map(() => ({ letter: '', active: false, color: '' })),
        locked: false,
        shake: false
      })),
      currentRow: 0,
      currentInputIndex: 0,
      gameOver: false,
      greetingMessage: ['Smartest', 'Very Clever', 'Good Job', 'Nice Work','You Got It','Nice', 'Maybe Next Time'],
      numberGreeting: 0,
      gameState : {
        answer: ["", "", "", "", "", ""],
        attempt: 0,
        enableFreeEdit: false,
        enableHardMode: false,
        enableHighContrast: false,
        enableLiarMode: false,
        lastCompletedDate: 0,
        LieBoxes: []
      },
      gameStats : {
        currentStreak: 0,
        distribution : {1:0, 2: 0, 3: 0, 4: 0, 5: 0, 6:0, fail:0},
        maxstreak: 0
      },
      totalPlaying : 0,
      persenWin: 0,
      winStreakNow: 0,
      winStreak: 0,
      highestValue: 0,
      finish: false
    }
  },
  mounted() {
    this.getDataKata().then((item) => {
    // console.log("word", this.wordOfTheDay);
    // const meaning = KBBI.cari(this.wordOfTheDay);
    // console.log("meaning", meaning)

    window.addEventListener('keydown', this.handleKeydown);
    if(localStorage.getItem("katla:invalidWords") != undefined ){this.invalidWords = JSON.parse(localStorage.getItem("katla:invalidWords"))}
    if( localStorage.getItem("katla:gameStats") != undefined) {
      this.gameStats = JSON.parse(localStorage.getItem("katla:gameStats"))
      this.countStatics();
      // console.log("Jalan")
    }
    if( localStorage.getItem("katla:gameState") != undefined){
      const timestamp =(new Date()).setHours(0,0,0,0)
      const tempGameState = JSON.parse(localStorage.getItem("katla:gameState"))
      // console.log("Check Date " + timestamp + " dan game state " + tempGameState.lastCompletedDate)
      if(tempGameState.lastCompletedDate != timestamp){
        localStorage.setItem("katla:invalidWords", JSON.stringify([]))
        localStorage.setItem("katla:gameState", undefined)
      } 
      this.gameState = JSON.parse(localStorage.getItem("katla:gameState"))
      
      this.insertLocalStorage();
    }
    }); 
  },
  beforeUnmount() {
    // if(localStorage.getItem("katla:invalidWords")){this.invalidWords = localStorage.getItem("katla:invalidWords")}
    window.removeEventListener('keydown', this.handleKeydown);
  },
  methods: {
    countStatics() {
      this.totalPlaying = 0
      for(let i=0;i<6;i++){
        if(this.highestValue < this.gameStats.distribution[i+1]) {
          this.highestValue = this.gameStats.distribution[i+1]
        }
        this.totalPlaying = this.totalPlaying + this.gameStats.distribution[i+1]
      }
      this.totalPlaying = this.totalPlaying + parseInt(this.gameStats.distribution.fail) 
      // this.totalPlaying = this.gameStats.distribution["1"] + this.gameStats.distribution["2"] +this.gameStats.distribution["3"] + this.gameStats.distribution["4"] + this.gameStats.distribution["5"] + this.gameStats.distribution["6"] + parseInt(this.gameStats.distribution.fail)
      this.persenWin = Math.round((this.totalPlaying  - this.gameStats.distribution.fail) / this.totalPlaying *100)
      this.winStreak = this.gameStats.maxstreak
      this.winStreakNow = this.gameStats.currentStreak
      return
    },
    insertLocalStorage() {
      for(let i=0; i<this.gameState.attempt; i++){
        if (this.firstCheckWord(i)) {
              this.gameOver = true;
              return;
            }
        this.currentRow++;
        console.log(this.rows[i])
      }
    },
    firstCheckWord(attempt) {
      let currentRow = this.rows[attempt]; 
      // console.log("checkWord", this.wordOfTheDay)
      const wordArray = this.wordOfTheDay.split('');
      let isExactMatch = true;
      currentRow.locked = true;
      // Create a copy to keep track of letters already matched
      const letterMatch = wordArray.slice();
      const letterLocalStorage = this.gameState.answer[attempt];

      // First pass to find exact matches
      currentRow.boxes.forEach((box, index) => {
        // console.log(letterLocalStorage[index] + " = " + this.wordOfTheDay[index] )
        if (letterLocalStorage[index] === this.wordOfTheDay[index]) {
          box.color = 'correct';
          box.letter =letterLocalStorage[index];
          letterMatch[index] = null; // Remove matched letter
          this.$refs.keyboard.updateKeyStatus(letterLocalStorage[index], 'correct');
        } else {
          isExactMatch = false;
        }
      });

      // Second pass to find correct letters in wrong positions
      currentRow.boxes.forEach((box, index) => {
        if (box.color !== 'correct') {
          if (letterMatch.includes(letterLocalStorage[index])) {
            box.color = 'present';
            box.letter =letterLocalStorage[index];
            letterMatch[letterMatch.indexOf(box.letter)] = null;
            this.$refs.keyboard.updateKeyStatus(letterLocalStorage[index], 'present');
          } else {
            box.color = 'absent';
            box.letter =letterLocalStorage[index];
            this.$refs.keyboard.updateKeyStatus(letterLocalStorage[index], 'absent');
          }
        }
      });

      // Flip the boxes after marking
      currentRow.boxes.forEach((box, boxIndex) => {
        setTimeout(() => {
          box.active = false;
        }, boxIndex * 300);
      });

      return isExactMatch;
    },
    async getDataKata() {  
      const timestamp =(new Date()).setHours(0,0,0,0)
      // console.log("try hit ", timestamp)
      const axiosWord = await axios.get(`https://tykozidane.com/api/katla/today`)
      // console.log("after hit", axiosWord.data.code)
        if (axiosWord.data.code != "00") {
          return new Error('Failed to fetch word of the day');
        }
        // const data = await axiosWord.data.json();
        this.wordOfTheDay = axiosWord.data.data.dataWord.word;
        this.numberOfDays = axiosWord.data.data.count
        return axiosWord.data.data.dataWord.word;
    },  
    handleKeydown(event) {
      const key = event.key || event;
      // console.log("handelKey",event)
      if (this.gameOver) return;

      if (this.currentRow >= this.rows.length) {
        return; // All rows are filled and locked
      }

      const currentRow = this.rows[this.currentRow];

      if (key === 'Enter') {
        if (this.currentInputIndex === 5) {
          const currentWord = this.rows[this.currentRow].boxes.map(box => box.letter).join('');
          const checkDoubleAnswer = this.gameState.answer.includes(currentWord)
          if(checkDoubleAnswer) {return;}
          if (!this.checkingKBBI()) {
            
            this.shakeRow();
            this.$refs.popup.show();
            // this.invalidWords.push("coba")
            this.invalidWords.push(currentWord)
            // localStorage.invalidWords = JSON.stringify(this.invalidWords)
            localStorage.setItem("katla:invalidWords", JSON.stringify(this.invalidWords))
            return;
          }
          currentRow.locked = true; // Lock the row before checking the word
          this.gameState.answer[this.gameState.attempt] = currentWord
          this.gameState.attempt++;
          const timestamp =(new Date()).setHours(0,0,0,0)
          this.gameState.lastCompletedDate = timestamp
          localStorage.setItem("katla:gameState", JSON.stringify(this.gameState))
          setTimeout(() => { // Check the word after the flip animation
            if (this.checkWord()) {
              this.gameOver = true;
              this.gameStats.distribution[this.currentRow+1] = this.gameStats.distribution[this.currentRow+1] + 1
              this.gameStats.currentStreak = this.gameStats.currentStreak + 1
              if(this.gameStats.maxstreak < this.gameStats.currentStreak){
                this.gameStats.maxstreak = this.gameStats.currentStreak;
              }
              
          localStorage.setItem("katla:gameStats", JSON.stringify(this.gameStats))
              this.countStatics();
              this.numberGreeting = this.currentRow
              this.$refs.finishedPopup.show();
              this.finish = true
              setTimeout(()=> {
              this.$refs.statisticPopup.show();
            }, 5000);
              return;
            }
            this.currentRow++;
            if(this.currentRow == 6) {
              if(this.gameStats.maxstreak < this.gameStats.currentStreak){
                this.gameStats.maxstreak = this.gameStats.currentStreak;
              }
              this.gameStats.currentStreak = 0
              this.gameStats.distribution.fail = this.gameStats.distribution.fail + 1
              console.log("fail", this.gameStats.distribution.fail)
              localStorage.setItem("katla:gameStats", JSON.stringify(this.gameStats))
              this.countStatics();
              this.numberGreeting = 6;
              this.$refs.finishedPopup.show();
              this.finish = true
              setTimeout(()=> {
              this.$refs.statisticPopup.show();
              }, 5000);
            }                                     
            
            this.currentInputIndex = 0;
          }, 500); // Match this with the flip animation duration
        }
      } else if (/^[a-zA-Z]$/.test(key)) {
        if (this.currentInputIndex < 5 && !currentRow.locked) {
          currentRow.boxes[this.currentInputIndex].letter = key.toLowerCase();
          currentRow.boxes[this.currentInputIndex].active = true;
          this.currentInputIndex++;
          // this.$refs.keyboard.updateKeyStatus(key.toLowerCase(), 'pressed');
        }
      } else if (key === 'Backspace') {
        if (this.currentInputIndex > 0 && !currentRow.locked) {
          this.currentInputIndex--;
          currentRow.boxes[this.currentInputIndex].letter = '';
          currentRow.boxes[this.currentInputIndex].active = false;
        }
      }
    },
    checkingKBBI() {
      const currentWord = this.rows[this.currentRow].boxes.map(box => box.letter).join('');
      return words.includes(currentWord);
    },
    checkWord() {
      const currentRow = this.rows[this.currentRow];
      const wordArray = this.wordOfTheDay.split('');
      let isExactMatch = true;

      // Create a copy to keep track of letters already matched
      const letterMatch = wordArray.slice();

      // First pass to find exact matches
      currentRow.boxes.forEach((box, index) => {
        if (box.letter === this.wordOfTheDay[index]) {
          box.color = 'correct';
          letterMatch[index] = null; // Remove matched letter
          this.$refs.keyboard.updateKeyStatus(box.letter, 'correct');
        } else {
          isExactMatch = false;
        }
      });

      // Second pass to find correct letters in wrong positions
      currentRow.boxes.forEach((box, index) => {
        if (box.color !== 'correct') {
          if (letterMatch.includes(box.letter)) {
            box.color = 'present';
            letterMatch[letterMatch.indexOf(box.letter)] = null;
            this.$refs.keyboard.updateKeyStatus(box.letter, 'present');
          } else {
            box.color = 'absent';
            this.$refs.keyboard.updateKeyStatus(box.letter, 'absent');
          }
        }
      });

      // Flip the boxes after marking
      currentRow.boxes.forEach((box, boxIndex) => {
        setTimeout(() => {
          box.active = false;
        }, boxIndex * 500);
      });

      return isExactMatch;
    },
    shakeRow() {
      const currentRow = this.rows[this.currentRow];
      currentRow.shake = true;
      setTimeout(() => {
        currentRow.shake = false;
      }, 600); // Shake duration should match the animation duration
    },
    showHowToPlay() {
      this.$refs.howtoplay.show();
    },
    showStatistic() {
      this.$refs.statisticPopup.show();
    }
  }
}
</script>

<style scoped>
.base::-webkit-scrollbar {
  display: none;
}

/* Hide scrollbar for IE, Edge and Firefox */
.base {
  -ms-overflow-style: none;  /* IE and Edge */
  scrollbar-width: none;  /* Firefox */
}
@media screen and ( min-width: 768px ) and ( min-height: 1024px ) {
 .base{
  padding-left: 40px;
  padding-right: 40px;
 } 
 .outerbox {
  max-width:none;
  max-height: 912px;
 }
}
@media screen and ( max-width: 1024px ) and ( max-height: 1023px ) {
 .base{
  padding-left: auto;
  padding-right: auto;
 } 
 .outerbox {
  max-width:28rem;
 }
}
@media screen and (min-aspect-ratio: 5/5) {
 .base{
  padding-left: auto;
  padding-right: auto;
 } 
 .outerbox {
  max-width: 28rem;
 }
}
/* @media (max-aspect-ratio: 5/5) {
 .base{
  padding-left: auto;
  padding-right: auto;
 } 
 .outerbox {
  max-width: 28rem;
 }
} */
.box {
  /* perspective: 1000px;
  width: 89px;
  height: 89px; */
  position: relative;
  transform-style: preserve-3d;
  transition: transform 2s;
}
.front, .back {
  /* width: 87px;
  height: 87px; */
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  backface-visibility: hidden;
  border-radius: 3px;
  /* font-size: 2.5rem; */
  transition: transform 2s, border 0s;
  text-transform: uppercase;
}
.front {
  background-color: #111827;
  border: 1px solid #41444b;
}
.back {
  background-color: #374151;
  transform: rotateX(180deg);
  border: none;
}
.back.correct {
  background-color: #15803d;
}
.back.present {
  background-color: #ca8a04;
}
.back.absent {
  background-color: #374151;
}

.box.flip {
  animation: flip 2s forwards;
}
.box.shake {
  animation: shake 0.6s;
}
@keyframes flip {
  0% { transform: rotateX(0deg); }
  100% { transform: rotateX(180deg); }
}
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 85% { transform: translateX(-10px); }
  20%, 40%, 60%, 80% { transform: translateX(10px); }
}
.front.animate {
  transform: scale(1);
  border: 0.5px solid #242c3c;
}
</style>
