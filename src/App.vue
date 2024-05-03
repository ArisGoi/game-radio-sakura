<template>
  <div id="game" ref="game" v-if="seconds !=0">
    <div class="counter" id="counter">{{ formattedTime }}</div>
    <div class="score">{{ score }}</div>

    <div 
      v-for="box in boxes" 
      :id="`box-${box.id}`"
      :key="box.id" 
      :class="['box', { 'move': box.moving, 'bomb': !box.test, 'flower': box.test }]" 
      @click="handleBoxClick(box)"
      :style="boxStyle(box)" 
    >
      <FlowerComponent v-if="box.test" />
      <BombComponent v-else />
    </div>
  </div>

  <EndPage v-if="seconds === 0" :score="score" :highScore="highScore" @restart="restartGame" />
</template>

<script>
import EndPage from './components/EndPage.vue';
import FlowerComponent from './components/FlowerComponent.vue';
import BombComponent from './components/BombComponent.vue';
import debounce from 'lodash/debounce';

export default {
  name: 'App',
  components: {
    EndPage,
    FlowerComponent,
    BombComponent
  },
  data() {
    return {
      score: 0,
      seconds: 98,
      boxes: [],
      gameInterval: null,
      highScore: this.getCookie('maxScore') || 0,
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
      this.seconds = 98;  // Reimposta il timer del gioco
      this.boxes = [];  // Svuota l'array dei box

      // Riavvia il gioco
      this.countdown();  // Riavvia il countdown del gioco
      this.runGame();  // Riavvia la generazione dei box

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
      };
    },

    handleBoxClick: debounce(function(box) {
      this.score += box.test ? 1 : -1;
      this.boxes = this.boxes.filter(b => b.id !== box.id);
    }, 100, {
      'leading': true,
      'trailing': false
    }),

    dropBox() {
      console.log(this.$refs.game);
      if (this.$refs.game) {
        const length = this.random(100, this.$refs.game.clientWidth - 100);
        const velocity = this.random(900, 10000);
        const size = this.random(50, 120);
        const test = Math.round(Math.random());
        const box = { id: Math.random(), size, position: length, velocity, test, moving: false };

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
          this.endGame();
          clearInterval(this.gameInterval);
        }
      };
      tick();
    },

    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(';').shift();
    },

    endGame() {
      if (!this.getCookie('maxScore')) {
        document.cookie = 'maxScore=0;path=/;max-age=31536000'; // Salva per 1 anno
      }
      if (this.score > this.maxScore) {
        this.highScore = this.score;
        document.cookie = `maxScore=${this.score};path=/;max-age=31536000`; // Salva per 1 anno
      }
    },
  },
  mounted() {
    this.$nextTick(() => {
      if (this.$refs.game) {
        this.countdown();
        this.runGame();
      } else {
        console.error('Game element is not yet available');
      }

      this.maxScore = this.getCookie('maxScore') || 0;
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
}

/* Reset */
* {
  margin: 0;
  padding: 0;
}

img {
  /* Disattiva il trascinamento delle immagini e la selezione */
  -webkit-user-drag: none;
  user-drag: none;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
}

#game{
  background: #efefef;
  background-image: url('@/assets/cover.jpg');
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  width:100%;
  height:100dvh;
  position: relative;
  overflow: hidden;
}

.score{
  font-size: 300px;
  opacity: .7;
  z-index: 20;
  font-family: arial;
  color: #daa5bd;
  user-select:none;
  width:100%;
  text-align:center;
  margin-top: 140px;
}

.box{
  will-change: transform;
  width: 120px;
  height: 120px;
  padding: 20px;
  margin: -20px;
  position:absolute;
  top: -140px;
  transition: transform 1.5s linear;
  cursor: pointer;
  z-index:100;
}

.box.flower{
  z-index: 1000;
}

.move{
  transform: translateY(120vh);
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
