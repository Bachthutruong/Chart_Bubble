<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    <ChatBubble />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import ChatBubble from "./components/ChatBubble.vue";

export default {
  name: 'App',
  components: {
    HelloWorld,
    ChatBubble
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
