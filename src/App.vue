<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <Code></Code>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import Code from "./components/Code.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
    Code
  }
};
</script>

<style lang="scss">
html,
body,
#app {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
