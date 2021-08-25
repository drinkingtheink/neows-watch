<template>
  <div id="app">
    <AppStage msg="Watchful Eye"/>
    <Earth />
  </div>
</template>

<script>
import AppStage from './components/AppStage.vue'
import Earth from './components/Earth.vue'

export default {
  name: 'App',
  components: {
    AppStage,
    Earth,
  }
}
</script>

<style lang="scss">
@import './styles/palette';
body {
  background-color: $black;
  margin: 0;
  padding: 0;
}

#app {
  height: 100vh;
  position: relative;
  overflow: hidden;
}
</style>
