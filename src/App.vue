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
/* .key-button {
  background-color: #6b7280;
  border-radius: 4px;
  padding: 10px;
  margin: 2px;
  cursor: pointer;
  font-size: 1rem;
  text-align: center;
  width: 40px;
  height: 45px;
  color: white;
  text-transform: uppercase;
}
.key-button.special {
  width: 60px;
}
.key-button.correct {
  background-color: #15803d;
}
.key-button.present {
  background-color: #ca8a04;
}
.key-button.input {
  background-color: #374151;
}
.key-button:hover {
  background-color: #5a636e;
} */

</style>
