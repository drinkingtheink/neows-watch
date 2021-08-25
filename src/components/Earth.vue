<template>
    <div class="earth">Earth</div>
</template>

<script>
export default {
    name: 'Earth',
}
</script>

<style lang="scss" scoped>
$earthDim: 110vw;

.earth {
    border-radius: 50%;
    background-color: green;
    height: $earthDim;
    width: $earthDim;
    position: absolute;
    bottom: -90rem;
}
</style>
