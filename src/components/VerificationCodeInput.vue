<template>
  <div class="flex space-x-2">
    <input
      v-for="(code, index) in codes"
      :key="index"
      type="text"
      maxlength="1"
      class="w-12 h-12 text-center text-xl border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
      v-model="codes[index]"
      @input="moveFocus(index)"
      @keydown.backspace="moveBack(index)"
      @keydown.enter="disableInputs"
      :disabled="isDisabled"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'VerificationCodeInput',
  setup() {
    const codes = ref(Array(5).fill(''));
    const isDisabled = ref(false);

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

    const disableInputs = () => {
      isDisabled.value = true;
    };

    return { codes, moveFocus, moveBack, disableInputs, isDisabled };
  },
});
</script>

<style scoped>
/* Tambahkan style khusus di sini jika diperlukan */
</style>
