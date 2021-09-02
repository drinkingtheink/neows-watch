<template>
    <div class="mobile-banner">
        <WELogo searching="true" />
        <p>- This application is best viewed on larger screens -</p>
    </div>
</template>

<script>
import WELogo from './WELogo.vue';

export default {
    name: 'MobileBanner',
    components: {
        WELogo,
    }
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';

.mobile-banner {
    background-color: rgba(black, 0.7);
    color: $yellow;
    padding: 2rem;
    display: flex;
    justify-content: center;
    align-content: center;
    flex-flow: column;
    height: 100vh;
    border: 20px solid $yellow;

    p {
        text-align: center;
    }
}
</style>
