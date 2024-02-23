<script>
export default {
  data: () => ({
    colors: ["red", "blue", "green", "yellow"],
    levels: [
      {
        name: "Лёгкий(Задержка 1.5с)",
        id: "easy",
        value: "easyLevel",
        delay: 1500,
      },
      {
        name: "Средний(Задержка 1с)",
        id: "normal",
        value: "normalLevel",
        delay: 1000,
      },
      {
        name: "Тяжёлый(Задержка 0.5с)",
        id: "hard",
        value: "hardLevel",
        delay: 500,
      },
    ],
    counter: 0,
    startGame: false,
    gameOver: false,
    sequence: [],
    userSequence: [],
    currentStep: 0,
    sounds: {
      green: new Audio("https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"),
      red: new Audio("https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"),
      blue: new Audio("https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"),
      yellow: new Audio(
        "https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"
      ),
    },
    selectedLevel: "easyLevel",
    delay: 1500,
  }),
  methods: {
    playSound(color) {
      if (this.sounds[color]) {
        this.sounds[color].play();
      }
    },
    handleClick(color) {
      if (this.startGame && !this.gameOver) {
        this.userSequence.push(color);
        if (
          this.userSequence[this.userSequence.length - 1] !==
          this.sequence[this.userSequence.length - 1]
        ) {
          this.gameOver = true;
        } else {
          if (this.userSequence.length === this.sequence.length) {
            this.counter++;
            this.userSequence = [];
            setTimeout(() => {
              if (!this.gameOver) {
                this.nextStep();
              }
            }, 1000);
          }
        }

        document.querySelector(`.${color}`).classList.add("click");
        setTimeout(() => {
          document.querySelector(`.${color}`).classList.remove("click");
        }, 200);
      }
    },
    startSimonGame() {
      this.startGame = true;
      this.gameOver = false;
      this.counter = 0;
      this.sequence = [];
      this.userSequence = [];

      const selectedLevel = this.levels.find(
        (level) => level.value === this.selectedLevel
      );

      const delay = selectedLevel ? selectedLevel.delay : 1500;

      this.nextStep(delay);
    },
    nextStep() {
      const randomColor =
        this.colors[Math.floor(Math.random() * this.colors.length)];
      this.sequence.push(randomColor);
      const selectedLevel = this.levels.find(
        (level) => level.value === this.selectedLevel
      );
      const delay = selectedLevel ? selectedLevel.delay : 1500;
      this.playSequence(delay);
    },
    playSequence(delay) {
      if (this.gameOver) {
        return;
      }

      this.sequence.forEach((color, index) => {
        setTimeout(() => {
          if (!this.gameOver) {
            this.activateColor(color);
          }
        }, index * delay);
        setTimeout(() => {
          if (!this.gameOver) {
            this.deactivateColor(color);
          }
        }, (index + 1) * delay - 200);
      });
    },
    activateColor(color) {
      document.querySelector(`.${color}`).classList.add("active");
      this.playSound(color);
    },
    deactivateColor(color) {
      document.querySelector(`.${color}`).classList.remove("active");
    },
  },
};
</script>

<template>
  <div id="app">
    <div class="simon">
      <div class="simon__buttons-block">
        <div
          v-for="(color, index) in colors"
          :key="index"
          :class="['simon__button', color]"
          @click="handleClick(color)"
        ></div>
      </div>
      <div class="simon__level">
        <div class="simon__counter">Score: {{ counter }}</div>
        <div
          v-for="(level, index) in levels"
          :key="index"
          class="simon__level-block"
        >
          <label>{{ level.name }}</label>
          <input
            type="radio"
            :id="level.id"
            :value="level.value"
            v-model="selectedLevel"
          />
        </div>
        <button class="simon__start" @click="startSimonGame">Start Game</button>
        <div v-if="gameOver" class="simon__game-over">Проиграл...</div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.simon {
  display: flex;
  align-items: center;
  gap: 20px;
  margin: 0 auto;
  width: max-content;

  &__buttons-block {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
  }

  &__button {
    width: 100px;
    height: 100px;
    margin: 10px;
    cursor: pointer;
    border-radius: 50%;
  }

  &__start {
    padding: 10px 10px;
    border: 1px solid rgba(97, 97, 97, 0.486);
    background-color: rgb(179, 80, 0);
    color: white;
    cursor: pointer;
  }

  &__level {
    display: grid;
    gap: 20px;
  }

  &__level-block {
    display: flex;

    input {
      cursor: pointer;
    }
  }
}

.red {
  background-color: red;
  opacity: 0.6;
}

.blue {
  background-color: blue;
  opacity: 0.6;
}

.green {
  background-color: green;
  opacity: 0.6;
}

.yellow {
  background-color: yellow;
  opacity: 0.6;
}
.active {
  opacity: 1;
}

.click {
  transform: scale(1.1);
  opacity: 1;
}

@media (max-width: 500px) {
  .simon {
    flex-direction: column;
  }
}
</style>
