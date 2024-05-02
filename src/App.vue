<template>
  <div id="game" ref="game" v-if="seconds !=0">
    <div class="counter" id="counter">{{ formattedTime }}</div>
    <div class="score">{{ score }}</div>

    <div 
      v-for="box in boxes" 
      :key="box.id" 
      :class="['box', { 'move': box.moving, 'bomb': !box.test, 'flower': box.test }]" 
      @click="handleBoxClick(box)"
      :style="boxStyle(box)" 
    />
  </div>

  <EndPage v-if="seconds === 0" :score="score" @restart="restartGame" />
</template>

<script>
import EndPage from './components/EndPage.vue';

export default {
  name: 'App',
  components: {
    EndPage
  },
  data() {
    return {
      score: 0,
      seconds: 90,
      boxes: [],
      gameInterval: null
    };
  },
  computed: {
    formattedTime() {
      return `${this.seconds < 10 ? '0' : ''}${this.seconds}s`;
    }
  },
  methods: {
    random(min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    },
    restartGame() {
      // Reset delle variabili di gioco
      this.score = 0;  // Resetta il punteggio
      this.seconds = 90;  // Reimposta il timer del gioco
      this.boxes = [];  // Svuota l'array dei box

      // Riavvia il gioco
      this.countdown();  // Riavvia il countdown del gioco
      this.runGame();  // Riavvia la generazione dei box

      // Si potrebbe aver bisogno di fermare il ciclo di gioco corrente prima di riavviarlo
      clearInterval(this.gameInterval);  // Ferma l'intervallo di gioco attuale per evitare duplicazioni

      // Imposta nuovamente l'intervallo di gioco
      this.gameInterval = setInterval(() => {
        for (let i = 0; i < 10; i++) {
          this.dropBox();
        }
      }, 5000);
    },
    boxStyle(box) {
      return {
        width: `${box.size}px`,
        height: `${box.size}px`,
        left: `${box.position}px`,
        transition: `transform ${box.velocity}ms linear`,
        backgroundImage: `url('${box.image}')`,
        backgroundSize: 'contain'
      };
    },
    handleBoxClick(box) {
      this.score += box.test ? 1 : -1;
      this.boxes = this.boxes.filter(b => b.id !== box.id);
    },
    dropBox() {
      console.log(this.$refs.game);
      if (this.$refs.game) {
        const length = this.random(100, this.$refs.game.clientWidth - 100);
        const velocity = this.random(850, 8000);
        const size = this.random(50, 150);
        const test = Math.round(Math.random());
        const image = test ? './flower.png' : './bomb.png';
        const box = { id: Math.random(), size, position: length, velocity, test, image, moving: false };

        this.boxes.push(box);
        
        setTimeout(() => {
          box.moving = true;
        }, this.random(0, 5000));

        // Calcolo quando il box esce dal campo visivo
        const transitionDuration = velocity + 5000; // 5000 = buffer di sicurezza
        setTimeout(() => {
          this.boxes = this.boxes.filter(b => b.id !== box.id);
        }, transitionDuration);
      } else {
        console.error('Game element is not yet available');
      }
    },
    runGame() {
      this.gameInterval = setInterval(() => {
        for (let i = 0; i < 10; i++) {
          this.dropBox();
        }
      }, 5000);
    },
    countdown() {
      const tick = () => {
        if (this.seconds > 0) {
          this.seconds--;
          setTimeout(tick, 1000);
        } else {
          // alert('Game over');
          clearInterval(this.gameInterval);
        }
      };
      tick();
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.countdown();
      this.runGame();
    });
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#game{
  background: #efefef;
  width:100%;
  height:750px;
  position: relative;
  overflow: hidden;
}

.score{
  font-size: 500px;
  z-index: 20;
  font-family: arial;
  color: #d7e4d9;
  user-select:none;
  width:100%;
  text-align:center;
}

.box{
  
  width: 100px;
  height:100px;
  position:absolute;
  top: -150px;
  transition: transform 1.5s linear;
  cursor: pointer;
  z-index:100;
}

.move{
  transform:translateY(120vh);
}

.counter{
  width:100%;
  margin: 0 auto;
  text-align:center;
  font-family:arial;
  color:red;
  font-weight:bold;
  padding-top:10px;
  font-size:20px;
  position:relative;
  z-index:9999;
}

</style>
