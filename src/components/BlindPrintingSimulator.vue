<template>
  <div class="mainText">
    <!-- <div class="first__message">Нажмите Enter чтобы начать</div> -->
    <h1>Тренажер слепой печати</h1>
    <span v-for="(char, idx) of startInfo" :key="idx" :class="{ greenSymb: idx === counterSuccess, redSymb: idx === counterErr }">
      {{ char }}
    </span>
    <div class="testTime">Время прохождения теста: {{ time }}</div>
    <div class="speed">Скорость: {{ speed }} зн/мин</div>
    <div class="accuracy">Точность: {{ accuracy }} %</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      startInfo: "",
      counterSuccess: 0,
      counterErr: null,
      badKeys: ["Shift", "CapsLock", "Tab", "Control", "Alt", "ArrowLeft", "ArrowRight", "ArrowDown", "ArrowUp"],
      time: 0,
      startFlag: false,
      speed: null,
      accuracy: 0,
      counter: 0,
    };
  },
  methods: {
    timeCounting() {
      this.time++;
    },
    speedCounting() {
      this.speed = Math.round((this.counterSuccess / this.time) * 60);
    },
    accuracyCounting() {
      const acc = (this.counterSuccess * 100) / this.counter;
      this.accuracy = acc.toFixed(1);
    },
    onClickHandler(evt) {
      const keyFlag = this.badKeys.includes(evt.key);
      if (!keyFlag) {
        this.counter++;
        if (!this.startFlag) {
          setInterval(this.timeCounting, 1000);
          setInterval(this.speedCounting, 100);
          setInterval(this.accuracyCounting, 100);
          this.startFlag = !this.startFlag;
        }
        const isContain = this.startInfo[this.counterSuccess].includes(evt.key);
        this.startFlag = true;
        if (isContain) {
          this.counterSuccess++;
          if (this.counterErr) {
            this.counterErr = null;
          }
        } else {
          this.counterErr = this.counterSuccess;
        }
      }
    },
  },
  async mounted() {
    try {
      let response = await fetch("https://baconipsum.com/api/?type=all-meat&sentences=1&start-with-lorem=1");
      response = await response.json();
      this.startInfo = response[0].split("");
    } catch (error) {
      console.error(error.message);
    }

    document.addEventListener("keydown", this.onClickHandler);
  },
};
</script>

<style scoped>
.mainText {
  /* max-width: 680px;
  width: 100%; */
  height: 225px;
  overflow: hidden;
  font-size: 21px;
  line-height: 32px;
}

span {
  min-width: 7px;
  min-height: 12px;
  display: inline-block;
}

.greenSymb {
  background: rgba(19, 221, 19, 0.781);
}

.redSymb {
  background: red;
}

.greenSymb,
.redSymb {
  padding: 3px;
  border-radius: 3px;
  color: #fff;
}
</style>
