<template>
  <div class="keyboard">
    <div class="keyboard-row" v-for="row in keys" :key="row[0]">
      <button 
        v-for="key in row" 
        :key="key" 
        @click="handleKeyClick(key)"
        class="key-button">
        {{ key }}
      </button>
    </div>
    <div class="keyboard-row">
      <button @click="handleKeyClick('Enter')" class="key-button special">Enter</button>
      <button @click="handleKeyClick('z')" class="key-button">z</button>
      <button v-for="key in ['x', 'c', 'v', 'b', 'n', 'm']" :key="key" @click="handleKeyClick(key)" class="key-button">{{ key }}</button>
      <button @click="handleKeyClick('Backspace')" class="key-button special">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="icon">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M18 12l-6-6m0 0l-6 6m6-6v12" />
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
        // Z moved to allow Enter button on its left
        // ['z', 'x', 'c', 'v', 'b', 'n', 'm'] 
      ]
    }
  },
  methods: {
    handleKeyClick(key) {
      this.$emit('key-press', key);
    }
  }
}
</script>

<style scoped>
.keyboard {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
  text-transform: uppercase;

}
.keyboard-row {
  display: flex;
  justify-content: center;
  margin: 5px 0;
}
.key-button {
  background-color: #6b7280;
  border: 1px solid #aaa;
  border-radius: 4px;
  padding: 10px;
  margin: 2px;
  cursor: pointer;
  font-size: 1rem;
  text-align: center;
  width: fit-content;
}
.key-button.special {
  background-color: #6b7280;
}
.key-button:hover {
  background-color: #bbb;
}
.icon {
  width: 20px;
  height: 20px;
}
</style>
