<template>
  <div class="container">
    <div class="text">
      <span
        v-for="(char, idx) of text"
        :key="idx"
        class="char"
        :class="{
          error: idx == error,
          passed: idx < counterSuccess,
        }"
        >{{ char }}</span
      >
    </div>
    <p>Время: {{ time }}</p>
    <p>Текущая скорость: {{ speed }} зн/мин</p>
    <p>Точность: {{ accuracy }} %</p>
    <button @click="resetTraining">Пройти заново</button>
    <!-- <p v-if="counterSuccess == text.length" class="congratulations">Поздравляю!</p> -->
  </div>
</template>

<script>
const CONTROL_KEYS = ["Shift", "CapsLock", "Tab", "Control", "Alt", "ArrowLeft", "ArrowRight", "ArrowDown", "ArrowUp"];

const initialState = () => ({
  text: "",

  counter: 0,
  counterSuccess: 0,
  error: null,

  time: 0,
  speed: 0,
  accuracy: 0,

  isStart: false,
});

export default {
  data() {
    return initialState();
  },
  methods: {
    /**
     * Fetch new training text from API.
     *
     */
    async getNewText() {
      try {
        let response = await fetch("https://baconipsum.com/api/?type=all-meat&sentences=1&start-with-lorem=1");
        response = await response.json();
        //response = ["aaa"];
        return response;
      } catch (error) {
        console.error(error.message);
      }
    },

    /**
     * Update current text.
     */
    async updateCurrentText(text) {
      this.text = text;
    },

    /**
     * Create timer and update time.
     */
    updateTime() {
      this.time++;
    },

    /**
     * Speed calculation.
     */
    updateSpeed(counterSuccess, time) {
      if (!counterSuccess || !time) return;
      this.speed = Math.round((counterSuccess / time) * 60);
    },

    /**
     * Accuracy calculation.
     */
    updateAccuracy(counterSuccess, counter) {
      if (!counterSuccess || !counter) return;
      const acc = (counterSuccess * 100) / counter;
      this.accuracy = acc.toFixed(1);
    },

    /**
     * Setup all our intervals.
     */
    startIntervalUpdate() {
      this.timeInterval = setInterval(() => this.updateTime(), 1000);
      this.speedInterval = setInterval(() => this.updateSpeed(this.counterSuccess, this.time), 100);
      this.accuracyInterval = setInterval(() => this.updateAccuracy(this.counterSuccess, this.counter), 100);
      // this.isStart = !this.isStart;
    },

    /**
     * Clear intervals.
     */
    clearIntervals() {
      if (this.timeInterval) {
        clearInterval(this.timeInterval);
      }

      if (this.speedInterval) {
        clearInterval(this.speedInterval);
      }

      if (this.accuracyInterval) {
        clearInterval(this.accuracyInterval);
      }
    },

    /**
     * Apply initial state.
     */
    resetState() {
      Object.assign(this.$data, initialState());
    },

    /**
     * Reset all.
     */
    async resetTraining() {
      this.resetState();

      let text = await this.getNewText();
      if (!text) return;

      let splittedText = text[0].split("");
      await this.updateCurrentText(splittedText);
    },

    /**
     * Init
     */
    async init() {
      let text = await this.getNewText();
      if (!text) return;

      let splittedText = text[0].split("");

      await this.updateCurrentText(splittedText);

      document.addEventListener("keydown", this.onKeydown);
    },

    /**
     * LISTENERS function.
     */
    onKeydown(event) {
      if (CONTROL_KEYS.includes(event.key)) return;

      // If our key.
      this.counter += 1;
      if (this.text[this.counterSuccess] && this.text[this.counterSuccess].includes(event.key)) {
        if (!this.isStart) {
          this.startIntervalUpdate();
          this.isStart = !this.isStart;
        }
        this.counterSuccess += 1;
        this.error = null;
      } else {
        this.error = this.counterSuccess;
      }
    },
  },
  watch: {
    counter(to) {
      if (to === this.text.length) {
        this.clearIntervals();
      }
    },
  },
  async mounted() {
    this.init();
  },
  destroyed() {
    document.removeEventListener("keydown", this.onKeydown);
    this.clearIntervals();
  },
};
</script>

<style scoped>
.container {
  max-width: 70vw;
  width: 100%;
  margin: 0 auto;

  font-size: 20px;
  line-height: 1.5;
  letter-spacing: 0.1em;

  transition: all 0.4s ease-out;
}

.text {
  padding-bottom: 40px;
  min-height: 80px;
}

p {
  font-size: 14px;
  line-height: 18px;
  padding-bottom: 10px;
}

p:last-of-type {
  padding-bottom: 20px;
}

.passed {
  background-color: green;
}

.error {
  background-color: red;
}

button {
  cursor: pointer;
  border: none;
  height: 35px;
  padding: 0 20px;
  border-radius: 4px;
  background-color: rgba(247, 244, 236, 1);
  transition: 0.3s;
}

button:hover {
  box-shadow: 4px 4px 35px 20px rgba(255, 255, 255, 0.2);
  transition: 0.3s;
}

/* .congratulations {
  font-size: 20px;
  text-transform: uppercase;
  color: red;
  padding: 10px;
  margin-top: 20px;
  text-align: center;
} */
</style>
