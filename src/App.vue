<template>
  <div id="app">
    <AppStage class="app-main" />
    <Earth class="gaia" />
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

  .app-main {
    z-index: 2;
  }

  .gaia {
    z-index: 1;
  }

  h1 {
    color: $yellow;
  }

  h3 {
    color: white;
  }
}
</style>
