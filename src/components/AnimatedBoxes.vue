<template>
  <div class="flex flex-col justify-center items-center h-screen bg-gray-900 text-white">
    <PopupMessage ref="popup" message="tidak ada di KBBI" />
    <PopupMessage ref="finishedPopup" :message="greetingMessage" :showCloseButton="true" />
    <div v-for="(row, rowIndex) in rows" :key="rowIndex" class="flex">
      <div v-for="(box, boxIndex) in row.boxes" :key="boxIndex" class="m-1">
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
    <KeyboardButtons @key-press="handleKeydown" />
  </div>
</template>

<script>
import { words } from '../assets/data.json';
import PopupMessage from './PopupMessage.vue';
import KeyboardButtons from './KeyboardButtons.vue';

export default {
  components: {
    PopupMessage,
    KeyboardButtons
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
          if (this.checkWord()) {
          currentRow.locked = true;
            this.gameOver = true;
            this.$refs.finishedPopup.show();
            return;
          }
          currentRow.locked = true;
          this.currentRow++;
          this.currentInputIndex = 0;
        }
      } else if (/^[a-zA-Z]$/.test(key)) {
        if (this.currentInputIndex < 5 && !currentRow.locked) {
          currentRow.boxes[this.currentInputIndex].letter = key.toLowerCase();
          currentRow.boxes[this.currentInputIndex].active = true;
          this.currentInputIndex++;
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
        } else {
          isExactMatch = false;
        }
      });

      // Second pass to find correct letters in wrong positions
      currentRow.boxes.forEach((box, index) => {
        if (box.color !== 'correct') {
          if (letterMatch.includes(box.letter)) {
            box.color = 'present';
            letterMatch[letterMatch.indexOf(box.letter)] = null; // Remove matched letter
          } else {
            box.color = 'absent';
          }
        }
      });

      return isExactMatch;
    },
    shakeRow() {
      const currentRow = this.rows[this.currentRow];
      currentRow.shake = true;
      setTimeout(() => {
        currentRow.shake = false;
      }, 600); // Shake duration should match the animation duration
    }
  }
}
</script>

<style scoped>
.box {
  perspective: 1000px;
  text-transform: uppercase;
  font-weight: 500;
  /* background-color: #111827; */
  
}
.front, .back {
  border-radius: 5px;
  width: 80px;
  height: 80px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  backface-visibility: hidden;
  border: 1px solid #242c3c;
  /* border-color: white; */
  font-size: 2.5rem;
  transition: transform 2s, border 0s;
}
.front {
  background-color:#111827;
}
.back {
  background-color: #374151;
  transform: rotateX(180deg);
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
.box {
  width: 80px;
  height: 80px;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 2s;
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
   10%, 30%, 50%, 70%, 90% { transform: translateX(-10px); }
   20%, 40%, 60%, 80% { transform: translateX(10px); }
}
.front.animate {
  transform: scale(1);
  border: 4px solid #374151;
}
</style>
