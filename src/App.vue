<template>
  <div id="app">
    <Simulator />
  </div>
</template>

<script>
import Simulator from "@/components/BlindPrintingSimulator";
export default {
  name: "App",
  components: {
    Simulator,
  },
};
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
.first__message {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: 400px;
  width: 100%;
  background: rgba(37, 37, 37, 0.4);
  padding: 30px 0;
}
</style>
