<template>
  <div class="keyboard">
    <div class="keyboard-row">
      <div class="flex">
        <button 
          v-for="key in keys[0]" 
          :key="key" 
          @click="handleKeyClick(key)"
          :class="['key-button', getKeyStatus(key)]"
          :style="getKeyStyle(key)">
          {{ key }}
        </button>
      </div>
    </div>
    <div class="keyboard-row">
      <div class="flex">
        <button 
          v-for="key in keys[1]" 
          :key="key" 
          @click="handleKeyClick(key)"
          :class="['key-button', getKeyStatus(key)]"
          :style="getKeyStyle(key)">
          {{ key }}
        </button>
      </div>
    </div>
    <div class="keyboard-row">
      <div class="flex">
      <button @click="handleKeyClick('Enter')" class="key-button special">Enter</button>
        <button 
          v-for="key in keys[2]" 
          :key="key" 
          @click="handleKeyClick(key)"
          :class="['key-button', getKeyStatus(key)]"
          :style="getKeyStyle(key)">
          {{ key }}
        </button>
      </div>
      <button @click="handleKeyClick('Backspace')" class="key-button special pl-4">
        <!-- <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M18 12l-6-6m0 0l-6 6m6-6v12" />
        </svg> -->
        <svg version="1.0" xmlns="http://www.w3.org/2000/svg"
 width="512.000000pt" height="512.000000pt" viewBox="0 0 512.000000 512.000000"
 preserveAspectRatio="xMidYMid meet" class="h-7 w-7 ">

<g transform="translate(0.000000,512.000000) scale(0.100000,-0.100000)"
fill="#ffff" stroke="none">
<path d="M1605 4313 c-67 -15 -128 -41 -187 -80 -60 -39 -119 -108 -624 -726
-410 -502 -565 -699 -587 -744 -59 -128 -59 -278 0 -406 22 -45 177 -242 587
-744 598 -732 595 -729 734 -785 l57 -23 1460 -3 c1057 -2 1477 0 1520 8 189
36 345 189 384 378 16 75 16 2669 0 2744 -37 177 -176 325 -348 370 -61 16
-177 17 -1521 16 -800 0 -1464 -2 -1475 -5z m2945 -335 c33 -17 51 -35 68 -68
l22 -44 0 -1306 0 -1306 -22 -44 c-17 -33 -35 -51 -68 -67 l-44 -23 -1427 0
-1426 0 -41 23 c-31 17 -164 173 -572 672 -292 358 -538 666 -547 685 -18 40
-13 108 10 142 8 13 252 313 542 668 404 495 536 650 567 668 l41 22 1426 0
1427 0 44 -22z"/>
<path d="M2476 3390 c-42 -13 -95 -68 -107 -112 -7 -27 -6 -51 1 -80 10 -37
51 -82 308 -340 l296 -298 -301 -302 c-291 -292 -301 -305 -308 -349 -17 -118
91 -214 202 -179 29 10 105 79 336 308 l297 297 298 -297 c230 -229 306 -298
335 -308 111 -35 219 61 202 179 -7 44 -17 57 -308 349 l-301 302 301 303
c291 291 301 304 308 348 13 89 -44 168 -132 185 -74 14 -81 8 -400 -309
l-303 -302 -297 297 c-249 247 -305 298 -338 307 -46 12 -49 12 -89 1z"/>
</g>
</svg>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'KeyboardButtons',
  data() {
    return {
      keys: [
        ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'],
        ['a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l'],
        ['z', 'x', 'c', 'v', 'b', 'n', 'm']
      ],
      keyStatuses: {}
    }
  },
  methods: {
    handleKeyClick(key) {
        this.$emit('key-press', key);
    },
    updateKeyStatus(key, status) {
      if (!this.keyStatuses[key] || (status === 'correct' && this.keyStatuses[key] !== 'correct')) {
        this.keyStatuses[key] = status;
      }
    },
    getKeyStatus(key) {
      return this.keyStatuses[key] ? this.keyStatuses[key] : '';
    },
    getKeyStyle(key) {
      return {
        backgroundColor: this.keyStatuses[key] ? this.keyStatuses[key] === 'correct' ? '#15803d' : this.keyStatuses[key] === 'present' ? '#ca8a04' : '#374151' : '#6b7280'
      }
    }
  }
}
</script>

<style scoped>
.keyboard {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 10px;
}
.keyboard-row {
  display: flex;
  justify-content: center;
  margin: 6px 0;
}
.key-button {
  background-color: #6b7280;
  color: white;
  border: none;
  width: 40px;
  height: 45px;
  text-transform: uppercase;
  border-radius: 4px;
  margin: 0 4px 0 4px;
}
.key-button.special {
  width: 60px;
}
</style>
