<template>
    <div class="body-dashboard-stage">
        <header>
            <h1>Watchful Eye | NEOWS Watching {{ bodyCount }} objects today</h1>
        </header>
        <div 
            v-for="body in bodyCollection" 
            :key="body.neo_reference_id"
            class="heavenly-body"
            :style="{ 'right': `${getRandomInt(10, 90)}%`, 'top':  `${getDistanceFromEarth(body.close_approach_data[0].miss_distance)}%`}"
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
        },
        getDistanceFromEarth(distData) {
            console.log(`DIST: ${JSON.stringify(distData)}`);
            return this.getRandomInt(20, 100);
        }
    }
}
</script>

<style lang="scss" scoped>
.body-dashboard-stage {
    height: 100vh;
}

header {
    display: flex;
    justify-content: center;
    align-content: center;
    background-color: rgba(black, 0.4);
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