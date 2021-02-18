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
a:active,
a:focus,
button:active,
button:focus,
form:active,
form:focus,
textarea:active,
textarea:focus {
  box-shadow: none;
  outline: none;
}

*,
*::before,
*::after {
  margin: 0;
  padding: 0;
}

html,
body {
  height: 100%;
}

#app {
  margin: 0;
  padding: 0;

  width: 100%;
  height: 100%;

  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #000000;

  font-family: Verdana, Geneva, Tahoma, sans-serif;
  color: rgba(247, 244, 236, 1);
}
</style>
