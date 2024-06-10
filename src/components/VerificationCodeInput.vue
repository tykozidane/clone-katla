<!-- src/components/VerificationCodeInput.vue -->
<template>
    <div class="flex space-x-2 text-black bg-white p-10">
      <h1>Hallo World</h1>
      <input
        v-for="(code, index) in codes"
        :key="index"
        type="text"
        maxlength="1"
        class="w-12 h-12 text-center text-xl border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
        v-model="codes[index]"
        @input="moveFocus(index)"
        @keydown.backspace="moveBack(index)"
      />
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref } from 'vue';
  
  export default defineComponent({
    name: 'VerificationCodeInput',
    setup() {
      const codes = ref(Array(6).fill(''));
  
      const moveFocus = (index: number) => {
        if (codes.value[index].length === 1 && index < codes.value.length - 1) {
          (document.querySelectorAll('input')[index + 1] as HTMLInputElement).focus();
        }
      };
  
      const moveBack = (index: number) => {
        if (codes.value[index] === '' && index > 0) {
          (document.querySelectorAll('input')[index - 1] as HTMLInputElement).focus();
        }
      };
  
      return { codes, moveFocus, moveBack };
    },
  });
  </script>
  
  <style scoped>
  /* Tambahkan style khusus di sini jika diperlukan */
  </style>
  