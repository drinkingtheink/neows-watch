<template>
    <div class="body-dashboard-stage">
        <div 
            v-for="body in bodyCollection" 
            :key="body.neo_reference_id"
            class="heavenly-body"
            :style="{ 'right': `${getRandomInt(10, 90)}%`, 'top':  `${getRandomInt(10, 90)}%`}"
        >
            <span>{{ body.name }}</span>
            <span class="disc" />
        </div>
    </div>
</template>

<script>
export default {
    name: 'BodyDashboard',
    props: {
        bodyCollection: Array,
        bodyCount: Number
    },
    methods: {
        getRandomInt(min, max) {
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }
    }
}
</script>

<style lang="scss" scoped>
.body-dashboard-stage {
    height: 100vh;
}

.heavenly-body {
    position: absolute;
}

$bodyDim: 20px;

.disc {
    border-radius: 50%;
    height: $bodyDim;
    width: $bodyDim;
    background-color: white;
    display: inline-block;
}
</style>