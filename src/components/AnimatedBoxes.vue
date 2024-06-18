<template>
  <div class="flex flex-col justify-center items-center h-screen bg-gray-900 text-white relative overflow-hidden">  
    <HowToPlay ref="howtoplay" />
    <HeaderComp @showHowToPlay="showHowToPlay" />
    <PopupMessage ref="popup" message="Tidak ada di KBBI" />
    <PopupMessage ref="finishedPopup" :message="greetingMessage" :showCloseButton="true" />
    <div class=" mx-auto max-w-full relative">
      
      <div v-for="(row, rowIndex) in rows" :key="rowIndex" class="grid grid-cols-5" :style="{ gap: '4px', marginTop: '4px' }">
      <div v-for="(box, boxIndex) in row.boxes" :key="boxIndex">
        <div
          class="box"
          :class="{ flip: row.locked, shake: row.shake }"
          :style="{ animationDelay: row.locked ? `${boxIndex * 0.5}s` : '0s' }"
        >
          <div class="front" :class="{ animate: box.active }">{{ box.letter }}</div>
          <div class="back" :class="box.color">{{ box.letter }}</div>
        </div>
      </div>
    </div>
    </div>
    
    <KeyboardButtons @key-press="handleKeydown" ref="keyboard" />
    <div class="mt-2 text-xs">
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

export default {
  components: {
    PopupMessage,
    KeyboardButtons,
    HeaderComp,
    HowToPlay
  },
  data() {
    return {
      wordOfTheDay: 'cinta',
      rows: Array(6).fill().map(() => ({
        boxes: Array(5).fill().map(() => ({ letter: '', active: false, color: '' })),
        locked: false,
        shake: false
      })),
      currentRow: 0,
      currentInputIndex: 0,
      gameOver: false,
      greetingMessage: 'Congratulations! You guessed the word!'
    }
  },
  mounted() {
    window.addEventListener('keydown', this.handleKeydown);
  },
  beforeUnmount() {
    window.removeEventListener('keydown', this.handleKeydown);
  },
  methods: {
    handleKeydown(event) {
      const key = event.key || event;

      if (this.gameOver) return;

      if (this.currentRow >= this.rows.length) {
        return; // All rows are filled and locked
      }

      const currentRow = this.rows[this.currentRow];

      if (key === 'Enter') {
        if (this.currentInputIndex === 5) {
          if (!this.checkingKBBI()) {
            this.shakeRow();
            this.$refs.popup.show();
            return;
          }
          currentRow.locked = true; // Lock the row before checking the word
          setTimeout(() => { // Check the word after the flip animation
            if (this.checkWord()) {
              this.gameOver = true;
              this.$refs.finishedPopup.show();
              return;
            }
            this.currentRow++;
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
    }
  }
}
</script>

<style scoped>
.box {
  perspective: 1000px;
  width: 89px;
  height: 89px;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 2s;
}
.front, .back {
  width: 87px;
  height: 87px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  backface-visibility: hidden;
  border-radius: 3px;
  font-size: 2.5rem;
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
