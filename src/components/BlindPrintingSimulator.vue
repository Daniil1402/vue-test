<template>
  <div class="mainText">
    <!-- <div class="first__message">Нажмите Enter чтобы начать</div> -->
    <h1>Тренажер слепой печати</h1>
    <span v-for="(char, idx) of startInfo" :key="idx" :class="{ greenSymb: idx === counter, redSymb: idx === counterErr }">
      {{ char }}
    </span>
  </div>
</template>

<script>
export default {
  data() {
    return {
      startInfo: "",
      counter: 0,
      counterErr: null,
    };
  },
  methods: {
    onClickHandler(evt) {
      const isContain = this.startInfo[this.counter].toLowerCase().includes(evt.key);
      console.log(isContain);
      if (isContain) {
        this.counter++;
        if (this.counterErr) {
          this.counterErr = null;
        }
      } else {
        this.counterErr = this.counter;
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
