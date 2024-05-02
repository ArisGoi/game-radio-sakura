<template>
  <div class="end-page">
    <h1>Game Over</h1>
    <p>Your score: {{ score }} points</p>
    <button @click="restartGame">Restart</button>
  </div>
</template>

<script>
export default {
  name: 'EndPage',
  props: {
    score: {
      type: Number,
      required: true
    }
  },
  methods: {
    restartGame() {
      this.$emit('restart');
    }
  }
}
</script>

<style scoped>
.end-page {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  font-family: 'Arial', sans-serif;
}
</style>
