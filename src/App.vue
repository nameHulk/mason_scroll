<template>
  <div id="app">
    <div style="width:600px;height:500px;">
      <MasonScroll>
          <div>000</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>123</div>
          <div>123</div><div>123</div><div>123</div><div>999</div>
      </MasonScroll>
    </div>
  </div>
</template>

<script>
import MasonScroll from './components/MasonScroll.vue'

export default {
  name: 'App',
  components: {
    MasonScroll
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
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
