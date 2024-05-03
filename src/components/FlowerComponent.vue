<template>
  <div class="flower">
    <img :src="petals[randomNumber]" alt="Flower">
  </div>
</template>

<script>
export default {
  name: 'FlowerComponent',
  data() {
    return {
      randomNumber: 0,
      petals: [
        require('@/assets/petal-1.png'),
        require('@/assets/petal-2.png'),
        require('@/assets/petal-3.png'),
        require('@/assets/petal-4.png'),
        require('@/assets/petal-5.png')
      ]
    };
  },
  mounted() {
    this.randomNumber = Math.floor(Math.random() * 5);
  }
}
</script>

<style scoped>
.flower {
  filter: drop-shadow(0 0 8px #0000002a);
  animation: rotate 5s linear infinite;
  box-sizing: border-box;
  height: calc(100% + 20px);
  width: calc(100% + 20px);
  padding: 20px;
  margin: -20px;
}
.flower img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

@keyframes rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
