<template>
  <div class="flex items-center justify-center h-screen">
    <VerificationCodeInput />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import './assets/main.css'
import VerificationCodeInput from './components/VerificationCodeInput.vue';

export default defineComponent({
  name: 'App',
  components: {
    VerificationCodeInput,
  },
});
</script>

<style>
/* Tambahkan style global di sini jika diperlukan */
</style>
