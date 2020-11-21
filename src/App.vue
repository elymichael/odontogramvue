<template>
  <div id="app">
    <div class="container">
      <Odontogram />
    </div>
  </div>
</template>

<script>
import Odontogram from "./components/Odontogram.vue";

export default {
  name: "App",
  components: {
    Odontogram
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 10px;
}
</style>
