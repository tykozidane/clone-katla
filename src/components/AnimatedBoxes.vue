<template>
  <div class="flex flex-col justify-start items-center h-screen bg-gray-900 text-white relative overflow-hidden">  
    <HowToPlay ref="howtoplay" />
    <StatisticPopup ref="statisticPopup" :gameStats="gameStats" :highestValue="highestValue" :totalPlaying="totalPlaying" :persenWin="persenWin" :winStreak="winStreak" :winStreakNow="winStreakNow" />
    <HeaderComp @showHowToPlay="showHowToPlay" @showStatistic="showStatistic" :numberOfDays="numberOfDays"/>
    <PopupMessage ref="popup" message="Tidak ada di KBBI" />
    <PopupMessage ref="finishedPopup" :message="greetingMessage" :showCloseButton="true" />
    <div class=" mx-auto max-w-lg grow-0 shrink grid grid-rows-6 gap-[6px] w-full aspect-[5/6] mt-3 my-2 px-2 mini:px-8" >    
      <div v-for="(row, rowIndex) in rows" :key="rowIndex" class="grid grid-cols-5 gap-[6px]">
      <div v-for="(box, boxIndex) in row.boxes" :key="boxIndex" class="w-full h-full">
        <div
          class="box w-full h-full font-bold mini:text-4xl text-2xl"
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
import firebaseConfig from '../../firebase.config.json';
import { initializeApp } from 'firebase/app';
import { collection, where, getFirestore, getDocs, doc, setDoc, addDoc, Timestamp } from 'firebase/firestore';
import { query } from 'firebase/database';
const fire = initializeApp(firebaseConfig);
var db = getFirestore(fire)

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
      greetingMessage: 'Congratulations! You guessed the word!',
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
    }
  },
  mounted() {
    this.getDataKata().then((item) => {
    // console.log("word", this.wordOfTheDay);
    // const meaning = KBBI.cari(this.wordOfTheDay);
    // console.log("meaning", meaning)

    window.addEventListener('keydown', this.handleKeydown);
    if(localStorage.getItem("katla:invalidWords").length > 0){this.invalidWords = JSON.parse(localStorage.getItem("katla:invalidWords"))}
    if(localStorage.getItem("katla:gameState").length > 0){
      const timestamp =(new Date()).setHours(0,0,0,0)
      const tempGameState = JSON.parse(localStorage.getItem("katla:gameState"))
      if(tempGameState.lastCompletedDate != timestamp){
        localStorage.setItem("katla:invalidWords", JSON.stringify([]))
        localStorage.setItem("katla:gameState", undefined)
      } 
      this.gameState = JSON.parse(localStorage.getItem("katla:gameState"))
      
      this.insertLocalStorage();
    }
    if(localStorage.getItem("katla:gameStats").length > 0) {
      this.gameStats = JSON.parse(localStorage.getItem("katla:gameStats"))
      for(let i=0;i<6;i++){
        if(this.highestValue < this.gameStats.distribution[i+1]) {
          this.highestValue = this.gameStats.distribution[i+1]
        }
        this.totalPlaying = this.totalPlaying + this.gameStats.distribution[i+1]
      }
      // this.totalPlaying = this.gameStats.distribution["1"] + this.gameStats.distribution["2"] +this.gameStats.distribution["3"] + this.gameStats.distribution["4"] + this.gameStats.distribution["5"] + this.gameStats.distribution["6"] + this.gameStats.distribution.fail
      this.persenWin = (this.totalPlaying  - this.gameStats.distribution.fail) / this.totalPlaying *100
      this.winStreak = this.gameStats.maxstreak
      this.winStreakNow = this.gameStats.currentStreak
      // console.log("Jalan")
    }
    }); 
  },
  beforeUnmount() {
    // if(localStorage.getItem("katla:invalidWords")){this.invalidWords = localStorage.getItem("katla:invalidWords")}
    window.removeEventListener('keydown', this.handleKeydown);
  },
  methods: {
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
      const wordArray = this.wordOfTheDay.split('');
      let isExactMatch = true;
      currentRow.locked = true;
      // Create a copy to keep track of letters already matched
      const letterMatch = wordArray.slice();
      const letterLocalStorage = this.gameState.answer[attempt];

      // First pass to find exact matches
      currentRow.boxes.forEach((box, index) => {
        console.log(letterLocalStorage[index] + " = " + this.wordOfTheDay[index] )
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
      console.log("try hit firebase", timestamp)
      const q = query(collection(db, "katla"), where('date', '>=', Timestamp.fromDate(new Date(timestamp))));
      const querySnapshot = await getDocs(q);
      // console.log(querySnapshot)
      // var successGet = false
      const q2 = query(collection(db, "katla"));
      const querySnapshot2 = await getDocs(q2);
      this.numberOfDays = querySnapshot2.size
      const dataKata = querySnapshot.docs.map((item)=>{return item.data()})
      console.log("size", dataKata)
      if(dataKata.length < 1){
          this.generateNewWord();
      } else {
        this.wordOfTheDay = dataKata[0].word
      }
      // querySnapshot.forEach((doc) => {
      //     // console.log(doc.data().date);
      //     const newDate = new Date(doc.data().date.seconds*1000)
      //     if(newDate >= timestamp){
      //       // console.log("Lebih besar", newDate)
      //       this.wordOfTheDay = doc.data().word
      //       successGet = true
      //     } 
      //   });
      //   if(!successGet){
      //     // this.generateNewWord();
      //   }
    },  
    async generateNewWord() {
      var success = false
      do {
        var randomNumebr = Math.floor(Math.random()*words.length)
        const newWord = words[randomNumebr];
        const q = query(collection(db, "katla"), where('word', '==', newWord));
        const querySnapshot = await getDocs(q);
        if(querySnapshot.empty){
          console.log("Tidak ada", newWord)
          const timestamp =(new Date()).setHours(0,0,0,0)
          await addDoc(collection(db, "katla"), {
            date: Timestamp.fromDate(new Date(timestamp)), word: newWord
          });
          // console.log("Document written with ID: ", docRef.id);
          // const katlaRef = collection(db, 'katla')
          // await setDoc(doc(katlaRef, ''), {
          //   date: timestamp, word: newWord
          // })
        } else {
          console.log("ada", newWord)
        }
        success = true
      } while (success == false)
    },
    handleKeydown(event) {
      const key = event.key || event;

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
              this.gameStats.distribution[this.currentRow]++
              this.gameStats.currentStreak++
              if(this.gameStats.maxstreak < this.gameStats.currentStreak){
                this.gameStats.maxstreak = this.gameStats.currentStreak;
              }
              
          localStorage.setItem("katla:gameStats", JSON.stringify(this.gameStats))

              this.$refs.finishedPopup.show();
              return;
            }
            this.currentRow++;
            if(this.currentRow == 6) {
              if(this.gameStats.maxstreak < this.gameStats.currentStreak){
                this.gameStats.maxstreak = this.gameStats.currentStreak;
              }
              this.gameStats.currentStreak = 0
              this.gameStats.distribution["fail"]++
              
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
  border: 0.5px solid #242c3c;
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
