/* eslint-disable no-unused-vars */
<template>
  <div id="app">
    <div class="game">
      <h1>Simon Says</h1>
      <div class="game-simon">
        <ul>
          <li
            class="blue cell"
            @click="getUserClick('color1')"
            ref="color1"
          ></li>
          <li
            class="red cell"
            @click="getUserClick('color2')"
            ref="color2"
          ></li>
          <li
            class="yellow cell"
            @click="getUserClick('color3')"
            ref="color3"
          ></li>
          <li
            class="green cell"
            @click="getUserClick('color4')"
            ref="color4"
          ></li>
        </ul>
      </div>
      <div class="game-info">
        <h2>Round: {{ rounds }}</h2>
        <button @click="startGame">Start</button>
        <div v-if="lose">
          <h3>Sorry you lose after {{ rounds - 1 }} rounds</h3>
        </div>
      </div>
      <div class="game-options">
        <h2>Game options</h2>
        <input type="radio" v-model="difficulty" name="mode" value="easy" />
        <label>Easy</label>
        <input type="radio" name="mode" value="medium" v-model="difficulty" />
        <label>Medium</label>
        <input type="radio" name="mode" value="hard" v-model="difficulty" />
        <label>Hard</label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => ({
    difficulty: "easy",
    rounds: 0,
    lose: false,
    isGame: false,
    colorsArray: [],
    userClicksArray: [],
  }),
  computed: {
    delay() {
      let delay = 0;

      switch (this.difficulty) {
        case "easy":
          delay = 1500;
          break;
        case "medium":
          delay = 1000;
          break;
        case "hard":
          delay = 400;
          break;
      }
      return delay;
    },
  },

  methods: {
    startGame() {
      if (!this.isGame) {
        this.isGame = true;
        this.rounds = 0;
        this.lose = false;
        this.startNewRound();
      }
    },

    startNewRound() {
      this.rounds += 1;
      this.userClicksArray = [];

      this.getRandomColor();

      this.animateColors();
    },

    animateColors() {
      let i = 0;
      let interval = setInterval(() => {
        this.playSound(this.colorsArray[i]);
        this.lightupItem(this.colorsArray[i]);

        i++;
        if (i >= this.colorsArray.length) {
          clearInterval(interval);
        }
      }, this.delay);
    },

    lightupItem(color) {
      let lightupItem = this.$refs[color];
      lightupItem.style.opacity = 1;
      setTimeout(() => {
        lightupItem.style.opacity = 0.5;
      }, this.delay / 2);
    },

    getRandomColor() {
      const colorIndex = Math.ceil(Math.random() * 4);
      const colorName = `color${colorIndex}`;

      this.colorsArray.push(colorName);
    },

    playSound(colorName) {
      const audio = new Audio(require(`./assets/sounds/${colorName}.ogg`));
      audio.play();
    },

    getUserClick(color) {
      this.userClicksArray.push(color);
      this.lightupItem(color);
      this.playSound(color);
      this.checkLenght();
    },

    checkLenght() {
      if (this.userClicksArray.length === this.colorsArray.length) {
        const equal = this.colorsArray.every(
          (color, index) => color === this.userClicksArray[index]
        );

        if (equal) {
          setTimeout(() => {
            this.startNewRound();
          }, 1000);
        } else {
          this.gameOver();
        }
      }
    },

    gameOver() {
      this.lose = true;
      this.isGame = false;
      this.userClicksArray = [];
      this.colorsArray = [];
    },
  },
};
</script>

<style lang="scss">
ul,
li {
  padding: 0;
  margin: 0;
}
.game {
  width: 540px;
  margin: 0 auto;

  h1 {
    margin-bottom: 5rem;
    text-align: center;
  }

  &-simon {
    background: #fff;
    position: relative;
    float: left;
    margin-right: 3em;
    width: 302px;
    height: 295px;
    border-radius: 150px 150px 150px 150px;
    box-shadow: 2px 1px 12px #aaa;
    .cell {
      opacity: 0.6;
      height: 290px;
      -webkit-border-radius: 150px 150px 150px 150px;
      border-radius: 150px 150px 150px 150px;
      position: absolute;

      &:hover {
        border: 2px solid black;
      }
    }

    .red {
      background: #ff5643;
      clip: rect(0px, 300px, 150px, 150px);
      width: 296px;
    }
    .blue {
      background: dodgerblue;
      clip: rect(0px, 150px, 150px, 0px);
      width: 300px;
    }
    .yellow {
      background: #feef33;
      clip: rect(150px, 150px, 300px, 0px);
      width: 300px;
    }
    .green {
      background: #bede15;
      clip: rect(150px, 300px, 300px, 150px);
      width: 296px;
    }
  }

  &-info {
    margin-top: 6rem;
    button {
      width: 5em;
      box-sizing: border-box;
      font-size: 1.4em;
      -webkit-border-radius: 10px 10px 10px 10px;
      border-radius: 10px 10px 10px 10px;
      background: #6dabe8;
      border: none;
      padding: 0.3em 0.6em;
    }
  }
}
</style>
