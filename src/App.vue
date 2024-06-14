<template>
  <div id="app">
    <AnimatedBoxes />
  </div>
</template>

<script>
import AnimatedBoxes from './components/AnimatedBoxes.vue'

export default {
  name: 'App',
  components: {
    AnimatedBoxes
  }
}
</script>

<style>
/* Add any global styles here if needed */
body {
  background-color: #111827;
  color: white;
}

</style>
