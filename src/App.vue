<template>
  <div id="app">
    <List/>
  </div>
</template>

<script>
import List from "./components/List";

export default {
  name: "App",
  components: {
    List
  }
};
</script>

<style>
html,
body {
  margin: 0;
}
#app {
  display: flex;
  justify-content: center;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  font-size: 1rem;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #00675b;
  margin: 0 auto;
  box-sizing: border-box;
}
input,
button {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  font-size: 1rem;
}
</style>
