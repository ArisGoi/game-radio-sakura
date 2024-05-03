<template>
  <div class="bomb">
    <img src="@/assets/bomb.png" alt="Bomb">
  </div>
</template>

<script>
export default {
  name: 'BombComponent'
}
</script>

<style scoped>
.bomb{
  filter: drop-shadow(0 0 8px #0000002a);
  animation: rotate 5s linear infinite;
  opacity: .9;
}

.bomb img {
  width: 100%;
  height: auto;
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
