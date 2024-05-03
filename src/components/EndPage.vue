<template>
  <div class="end-page">
    <h1>Game Over</h1>
    <p>Your score: {{ score }}</p>
    <p>High score: 🌟 {{ highScore }}</p>
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
    },
    highScore: {
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
  gap: 10px;
  align-items: center;
  height: 100vh;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 18px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
button:active {
  background-color: #3e8e41;
}
button:focus {
  outline: none;
}

h1 {
  font-size: 36px;
  color: #333;
}

p {
  font-size: 24px;
  color: #666;
}
</style>
