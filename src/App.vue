<template>
<div>
  <div id="app">
    <AppStage class="app-main" />
  </div>
</div>
</template>

<script>
import AppStage from './components/AppStage.vue'

export default {
  name: 'App',
  components: {
    AppStage,
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

  h1, h2, h3, h4, h5, h6 {
    font-family: $headerFont;
  }
}

#app {
  height: 100vh;
  position: relative;
  overflow: hidden;

  .app-main {
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
