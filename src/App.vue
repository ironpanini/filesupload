<template>
  <div id="app">
    <strong>大文件上传</strong>
    <Paste msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Paste from './components/Paste.vue'

export default {
  name: 'app',
  components: {
    Paste
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
