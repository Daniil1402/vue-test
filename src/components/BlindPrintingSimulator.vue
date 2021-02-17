<template>
  <div class="mainText">
    <h1>Тренажер слепой печати</h1>
    <span v-for="(char, idx) of text" :key="idx" :class="{ greenSymb: idx === counterSuccess, redSymb: idx === counterErr, passed: idx <= counterSuccess - 1 }">
      {{ char }}
    </span>
    <div class="testTime">Время прохождения теста: {{ time }}</div>
    <div class="speed">Скорость: {{ speed }} зн/мин</div>
    <div class="accuracy">Точность: {{ accuracy }} %</div>
    <div class="reload__image" v-on:click="reloadPage">
      <img src="@/assets/redo-solid.svg" alt="Reload" />
    </div>
    <div class="popup" v-if="isEnd">
      <h3 class="popup__title">Позравляю!!!</h3>
      <div class="testTime">Время прохождения теста: {{ time }}</div>
      <div class="speed">Скорость: {{ speed }} зн/мин</div>
      <div class="accuracy">Точность: {{ accuracy }} %</div>
      <div>
        <button v-on:click="reloadPage">Пройти заново</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: "",
      counterSuccess: 0,
      counterErr: null,
      time: 0,
      isStart: false,
      isEnd: false,
      speed: 0,
      accuracy: 0,
      counter: 0,
    };
  },
  methods: {
    updateTime() {
      this.time++;
    },
    updateSpeed(counterSuccess, time) {
      if (!counterSuccess || !time) return;
      this.speed = Math.round((counterSuccess / time) * 60);
    },
    updateAccuracy() {
      const acc = (this.counterSuccess * 100) / this.counter;
      this.accuracy = acc.toFixed(1);
    },
    onClickHandler(evt) {
      const keyFlag = this.controlKeys.includes(evt.key);
      if (!keyFlag) {
        this.counter++;
        const isContain = this.text[this.counterSuccess].includes(evt.key);
        if (isContain) {
          this.counterSuccess++;
          this.counterErr = null;
          if (this.counterSuccess == this.text.length) {
            this.stopUpdate();
          }
        } else {
          this.counterErr = this.counterSuccess;
        }
        if (!this.isStart) {
          this.startUpdate();
        }
      }
    },
    startUpdate() {
      this.timeInterval = setInterval(() => this.updateTime(), 1000);
      this.speedInterval = setInterval(() => this.updateSpeed(this.counterSuccess, this.time), 100);
      this.accuracyInterval = setInterval(() => this.updateAccuracy(), 100);
      this.isStart = !this.isStart;
    },
    stopUpdate() {
      clearInterval(this.timeInterval);
      clearInterval(this.speedInterval);
      clearInterval(this.accuracyInterval);
      document.removeEventListener("keydown", this.onClickHandler);
      // console.log("THE END");
      this.isEnd = true;
    },
    reloadPage() {
      window.location.reload();
    },
  },
  async mounted() {
    try {
      let response = await fetch("https://baconipsum.com/api/?type=all-meat&sentences=1&start-with-lorem=1");
      response = await response.json();
      //response = ["aaa"];
      this.text = response[0].split("");
    } catch (error) {
      console.error(error.message);
    }

    document.addEventListener("keydown", this.onClickHandler);
  },
  created() {
    this.controlKeys = ["Shift", "CapsLock", "Tab", "Control", "Alt", "ArrowLeft", "ArrowRight", "ArrowDown", "ArrowUp"];
  },
};
</script>

<style scoped>
.mainText {
  width: 100%;
  overflow: hidden;
  font-size: 21px;
  line-height: 32px;
}

span {
  min-width: 7px;
  line-height: 1.5;
  display: inline-flex;
  position: relative;
}

span::after {
  content: "";
  position: absolute;
  z-index: -1;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  border-radius: 3px;
}

.greenSymb::after {
  background: rgba(19, 221, 19, 0.781);
}

.redSymb::after {
  background: red;
}

.greenSymb,
.redSymb {
  padding: 0 3px;
  color: #fff;
  min-width: 7px;
  min-height: 10px;
  color: white;
}

.passed {
  color: rgb(55, 194, 62);
}

.reload__image {
  width: 35px;
  height: 35px;
  margin: 20px auto 0;
  cursor: pointer;
}

.reload__image img {
  width: 100%;
  height: 100%;
  display: block;
}

.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.popup > div {
  padding: 10px;
  background: #fff;
  color: #000;
  max-width: 400px;
  width: 100%;
}

.popup h3 {
  padding: 10px;
  background: #fff;
  max-width: 400px;
  width: 100%;
  margin: 0;
}
</style>
