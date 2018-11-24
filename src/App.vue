<template>
  <div id="app">
    <div class="main">
      <router-view/>
    </div>
    <left-nav></left-nav>
  </div>
</template>

<script>
import leftNav from '@/components/common/leftNav'
export default {
  name: 'App',
  components: {
    leftNav
  }
}
</script>

<style>
#app {
  font-family: 'Microsoft YaHei','Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.main {
  float: right;
  width: 95%;
  background-color: #EFF2F7;
  height: 100%;
  overflow: hidden;
}
</style>
