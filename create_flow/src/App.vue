<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <CreateFlow msg="Welcome to Your Vue.js App"/>
</template>

<script>
import CreateFlow from './components/CreateFlow.vue'

export default {
  name: 'App',
  components: {
    CreateFlow
  },
  mounted() {
    const flowsdk = document.createElement("script");
    flowsdk.setAttribute(
      "src",
      "https://usone.grouphour.com/moxo/monthlysearch/mepportal/flowsdk/dist/flowsdk.js"
    );
    flowsdk.async = false;
    document.head.appendChild(flowsdk);
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
