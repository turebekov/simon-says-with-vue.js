<template>
  <div id="app">

    <SimonSays msg="Simon Says"/>
  </div>
</template>

<script>

import SimonSays from "./components/SimonSays";

export default {
  name: 'App',
  components: {
      SimonSays
  }
}
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
</style>
