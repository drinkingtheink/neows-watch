<template>
    <div class="body-dashboard-stage">
        <header>
            <h1>Watchful Eye | NEOWS Watching {{ bodyCount }} objects today</h1>
        </header>
        <Body 
            v-for="body, index in bodyCollection" 
            :key="body.neo_reference_id"
            :style="{ 'right': `${getRandomInt(10, 90)}%`, 'top':  `${getDistanceFromEarth(body.close_approach_data[0].miss_distance)}%`, 'animation-delay': `0.${index}s`}"
            :getRandomInt="getRandomInt"
            :getDistanceFromEarth="getDistanceFromEarth"
            :body="body"
        >
        </Body>
    </div>
</template>

<script>
import Body from './Body.vue'

export default {
    name: 'BodyDashboard',
    components: {
        Body,
    },
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
            let distance = distData.miles;
            console.info(`DISTANCE: ${Math.floor(distance)}`);
            
            // let distanceSetting;
            
            return this.getRandomInt(15, 90);
        }
    }
}
</script>

<style lang="scss">
@import '../styles/palette';

.body-dashboard-stage {
    height: 100vh;

    header {
        display: flex;
        justify-content: center;
        align-content: center;
        background-color: rgba(black, 0.4);
    }
}
</style>