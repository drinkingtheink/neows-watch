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
@import url('https://fonts.googleapis.com/css2?family=Montserrat&family=Rubik:wght@300;400;500;600;700&display=swap');

$bodyFont: 'Montserrat', sans-serif;
$headerFont: 'Rubik', sans-serif;

body {
  background-color: $black;
  margin: 0;
  padding: 0;

  p, span, li {
    font-family: $bodyFont;
  }

  h1, h2, h3, h4, h5 {
    font-family: $headerFont;
  }
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
