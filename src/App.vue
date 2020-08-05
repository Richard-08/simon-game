<template>
  <div id="app">
    <div class="game">
      <div
        class="game__button"
        :class="[btn.name, { active: btn.active }]"
        v-for="(btn, index) in game"
        :key="index"
        @click="playSound(index)"
      ></div>

      <div class="game__controls">
        <h1 class="game__title">SIMON</h1>
        <button class="game__start-btn" @click.stop="startGame">Start</button>

        <div class="game__level">
          <select class="game__level-select" v-model="level">
            <option value="easy">Easy</option>
            <option value="medium">Medium</option>
            <option value="hard">Hard</option>
          </select>
        </div>
        <div class="game__score">
          <p>
            Score: <span>{{ count }}</span>
          </p>
        </div>
      </div>
    </div>

    <div class="modal" :class="{ active: gameOver }">
      <h2>You Lose</h2>
      <h3>Score: {{ count - 1 }}</h3>
      <button class="restart-btn" @click="restartGame">Try again</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      level: 'medium',
      round: 1,
      gameSteps: [],
      playerSteps: [],
      count: 0,
      gameOver: false,
      game: [
        {
          name: 'green',
          active: false,
          sound: new Audio(
            'https://s3.amazonaws.com/freecodecamp/simonSound1.mp3'
          ),
        },
        {
          name: 'red',
          active: false,
          sound: new Audio(
            'https://s3.amazonaws.com/freecodecamp/simonSound2.mp3'
          ),
        },
        {
          name: 'yellow',
          active: false,
          sound: new Audio(
            'https://s3.amazonaws.com/freecodecamp/simonSound3.mp3'
          ),
        },
        {
          name: 'blue',
          active: false,
          sound: new Audio(
            'https://s3.amazonaws.com/freecodecamp/simonSound4.mp3'
          ),
        },
      ],
    };
  },
  computed: {
    gameLevel() {
      if (this.level === 'easy') {
        return 1500;
      } else if (this.level === 'medium') {
        return 1000;
      } else {
        return 400;
      }
    },
  },
  methods: {
    startGame() {
      // Start game
      this.gameSteps = [];
      this.playerSteps = [];
      this.count = 0;
      this.round = 1;

      setTimeout(() => {
        this.addStep();
        this.count = this.gameSteps.length;
        this.replaySteps();
      }, 1000);
    },
    addStep() {
      // Add random steps
      this.gameSteps = [];
      for (let i = 1; i <= this.round; i += 1) {
        const index = Math.floor(Math.random() * this.game.length);
        this.gameSteps.push(index);
      }
    },
    replaySteps() {
      // Show game steps
      this.playerSteps = [];
      this.gameSteps.forEach((value, index) => {
        setTimeout(() => {
          this.game[value].sound.play();
          this.game[value].active = true;

          setTimeout(() => {
            this.game[value].active = false;
          }, 300);
        }, this.gameLevel * (index + 1));
      });
    },
    playSound(index) {
      // Play and check player choice
      if (!this.gameSteps.length) {
        return;
      }

      this.game[index].sound.play();
      this.playerSteps.push(index);

      if (
        this.gameSteps[this.playerSteps.length - 1] !==
        this.playerSteps[this.playerSteps.length - 1]
      ) {
        this.wrongSound();
        this.gameOver = true;
      } else {
        if (this.gameSteps.length === this.playerSteps.length) {
          setTimeout(() => {
            this.count += 1;
            this.round += 1;
            this.addStep();
            this.replaySteps();
          }, 1000);
        }
      }
    },
    wrongSound() {
      this.game.forEach((value) => value.sound.play());
    },
    restartGame() {
      this.gameOver = false;
      this.startGame();
    },
  },
};
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  border: none;
  text-decoration: none;
  font-family: 'Roboto', sans-serif;
}

#app {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
  min-height: 100vh;
  background-color: #2c3e50;

  display: flex;
  align-items: center;
  justify-content: center;
}

.game {
  background-color: #2c3e50;
  max-width: 100%;
  width: 60vh;
  height: 60vh;
  position: relative;

  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: auto;
  grid-gap: 10px;

  &__button {
    cursor: pointer;
    transition: all 0.2s ease;
  }

  &__controls {
    background-color: #2c3e50;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 45%;
    height: 45%;
    border-radius: 50%;

    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  &__title {
    font-size: 25px;
  }

  &__start-btn {
    padding: 5px;
    font-weight: 700;
    text-transform: uppercase;
    background-color: #27ae60;
    border-radius: 5px;
    color: #fff;
    cursor: pointer;
    margin: 8px 0;
    transition: all 0.2s ease;

    &:hover {
      background-color: #fff;
      color: #27ae60;
    }
  }

  &__level {
    margin-bottom: 5px;
  }

  &__level-select {
  }

  &__score {
    span {
      font-weight: 700;
    }
  }

  .green {
    background-color: #2ecc71;
    border-radius: 100% 0 0 0;
    animation: ligthGreen 0.5s ease 0s;

    @keyframes ligthGreen {
      100% {
        background-color: hsl(145, 100%, 49%);
        box-shadow: 0px 0px 10px 5px hsl(145, 100%, 49%);
      }
    }
  }

  .red {
    background-color: #e74c3c;
    border-radius: 0 100% 0 0;
    animation: ligthRed 0.5s ease 0.5s;

    @keyframes ligthRed {
      100% {
        background-color: hsl(6, 100%, 57%);
        box-shadow: 0px 0px 10px 5px hsl(6, 100%, 57%);
      }
    }
  }

  .yellow {
    background-color: #f1c40f;
    border-radius: 0 0 0 100%;
    animation: ligthYellow 0.5s ease 1.5s;

    @keyframes ligthYellow {
      100% {
        background-color: hsl(48, 100%, 50%);
        box-shadow: 0px 0px 10px 5px hsl(48, 100%, 50%);
      }
    }
  }

  .blue {
    background-color: #3498db;
    border-radius: 0 0 100% 0;
    animation: ligthBlue 0.5s ease 1s;

    @keyframes ligthBlue {
      100% {
        background-color: hsl(204, 100%, 53%);
        box-shadow: 0px 0px 10px 5px hsl(204, 100%, 53%);
      }
    }
  }

  $active-colors: (
    green: hsl(145, 100%, 49%),
    red: hsl(6, 100%, 57%),
    yellow: hsl(48, 100%, 50%),
    blue: hsl(204, 100%, 53%),
  );

  @each $name, $color in $active-colors {
    .#{$name}.active {
      background-color: $color;
      transform: scale(0.95);
      box-shadow: 0px 0px 10px 5px $color;
    }
  }

  @each $name, $color in $active-colors {
    .#{$name}:active {
      background-color: $color;
      transform: scale(0.95);
      box-shadow: 0px 0px 10px 5px $color;
    }
  }
}

.modal {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 60vh;
  height: 60vh;
  transform: translate(-50%, -50%) scale(0);
  transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  background-color: rgba(255, 255, 255, 0.8);
  color: #2c3e50;
  padding: 10px;
  border-radius: 50%;

  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;

  h2 {
    font-size: 35px;
  }

  h3 {
    font-size: 25px;
    margin: 10px;
  }

  .restart-btn {
    padding: 10px;
    border-radius: 5px;
    background-color: #2ecc71;
    color: #fff;
    font-weight: 700;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s ease;

    &:hover {
      background-color: #fff;
      color: #2ecc71;
    }
  }
}

.modal.active {
  transform: translate(-50%, -50%) scale(1);
}

@media screen and (max-width: 768px) {
  .game {
    width: 50vh;
    height: 50vh;
  }

  .modal {
    width: 50vh;
    height: 50vh;
  }
}

@media screen and (max-width: 400px) {
  .game__title {
    font-size: 20px;
  }
}
</style>
