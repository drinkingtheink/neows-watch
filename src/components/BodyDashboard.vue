<template>
    <div class="body-dashboard-stage">
        <transition-group name="asteroid" tag="div" class="asteroid-container">
            <Body
                v-for="(body, index) in bodyCollection"
                :key="body.neo_reference_id"
                :style="{ 'right': `${getRandomInt(10, 80)}%`, 'top': `${getDistanceFromEarth(body.close_approach_data[0].miss_distance, body.is_potentially_hazardous_asteroid)}%`, 'animation-delay': `0.${index}s`}"
                :body="body"
                :bodyIndex="index"
            >
            </Body>
        </transition-group>
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
        bodyCount: Number,
    },
    methods: {
        getRandomInt(min, max) {
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },
        getDistanceFromEarth(distData, posesThreat) {
            let distance = Math.floor(distData.miles);
            let bodyMin;
            let bodyMax;
            let floor = 1000000;
            let medFloor = 150000;
            let ceiling = 20000000;

            if (distance < floor || posesThreat) {
                bodyMin = 45;
                bodyMax = 50;
            }

            else if (distance > floor && distance < medFloor) {
                bodyMin = 55;
                bodyMax = 65;
            }

            else if (distance > medFloor && distance < ceiling) {
                bodyMin = 45;
                bodyMax = 55;
            }

            else if (distance > ceiling) {
                bodyMin = 10;
                bodyMax = 25;
            }

            else {
                bodyMin = 45;
                bodyMax = 55;
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

.asteroid-container {
    position: relative;
    height: 100%;
    width: 100%;
}

// Exit animation - asteroids fly off toward Earth (left)
.asteroid-leave-active {
    transition: all 0.8s ease-in;
}

.asteroid-leave-to {
    transform: translateX(-200%) scale(0.5);
    opacity: 0;
}

// Ensure entering asteroids still use their original animation
.asteroid-enter-active {
    // The enter animation is handled by Body.vue's appear animation
}
</style>