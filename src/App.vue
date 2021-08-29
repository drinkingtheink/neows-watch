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
@import './styles/typography';

body {
  background-color: $darkerpurple;
  margin: 0;
  padding: 0;

  p, span, li, small {
    font-family: $bodyFont;
  }

  h1, h2, h3, h4, h5, h6, label {
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

  label {
    text-transform: uppercase;
  }

  button:not(.heavenly-body) {
    color: $black;
    background: $yellow;
    border: 2px solid $yellow;
    border-radius: 2px;
    padding: 1rem 2rem;
    text-transform: uppercase;
    font-size: 120%;
    transition: all .2s;
    border-radius: 20px;

    &:hover {
        cursor: pointer;
        background: black;
        color: $yellow;
        border-color: $yellow;
    }
  }
}

.modal-fade-enter,
.modal-fade-leave-to {
    opacity: 0;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
    transition: opacity .5s ease;
}
</style>
