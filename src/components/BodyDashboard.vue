<template>
    <div class="body-dashboard-stage">
        <Body 
            v-for="body, index in bodyCollection" 
            :key="body.neo_reference_id"
            :style="{ 'right': `${getRandomInt(10, 90)}%`, 'top': `${getDistanceFromEarth(body.close_approach_data[0].miss_distance)}%`, 'animation-delay': `0.${index}s`}"
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
            let distance = Math.floor(distData.miles);
            let bodyMin;
            let bodyMax;
            let floor = 1000000;
            let medFloor = 150000;
            let ceiling = 20000000;

            if (distance < floor) {
                bodyMin = 75;
                bodyMax = 85;
            }

            else if (distance > floor && distance < medFloor) {
                bodyMin = 55;
                bodyMax = 70;
            }

            else if (distance > medFloor && distance < ceiling) {
                bodyMin = 60;
                bodyMax = 25;
            }

            else if (distance > ceiling) {
                bodyMin = 15;
                bodyMax = 25;
            }

            else {
                bodyMin = 85;
                bodyMax = 90;
            }
            
            return this.getRandomInt(bodyMin, bodyMax);
        }
    }
}
</script>

<style lang="scss">
@import '../styles/palette';

.body-dashboard-stage {
    height: 100vh;
}
</style>