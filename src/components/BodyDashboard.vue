<template>
    <div class="body-dashboard-stage">
        <transition-group name="asteroid" tag="div" class="asteroid-container" :css="true">
            <Body
                v-for="(body, index) in bodyCollection"
                :key="body.neo_reference_id"
                :style="{ 'right': `${getPosition(body, index).right}%`, 'top': `${getPosition(body, index).top}%`, 'animation-delay': `0.${index}s`}"
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
    data() {
        return {
            calculatedPositions: {},
            minDistance: 12, // Minimum distance between asteroid centers (in %)
        };
    },
    watch: {
        bodyCollection: {
            handler() {
                this.calculateAllPositions();
            },
            immediate: true,
        },
    },
    methods: {
        calculateAllPositions() {
            const positions = {};
            const placedPositions = [];

            if (!this.bodyCollection) return;

            this.bodyCollection.forEach((body, index) => {
                const distData = body.close_approach_data[0].miss_distance;
                const posesThreat = body.is_potentially_hazardous_asteroid;

                // Get vertical range based on distance
                const topRange = this.getTopRange(distData, posesThreat);

                // Find a non-overlapping position
                let position = this.findNonOverlappingPosition(topRange, placedPositions, index);

                positions[body.neo_reference_id] = position;
                placedPositions.push(position);
            });

            this.calculatedPositions = positions;
        },

        getTopRange(distData, posesThreat) {
            let distance = Math.floor(distData.miles);
            let floor = 1000000;
            let medFloor = 150000;
            let ceiling = 20000000;

            // Close or threatening asteroids - middle of screen
            if (distance < floor || posesThreat) {
                return { min: 30, max: 40 };
            }
            // Medium-close distance
            else if (distance > floor && distance < medFloor) {
                return { min: 35, max: 45 };
            }
            // Medium-far distance
            else if (distance > medFloor && distance < ceiling) {
                return { min: 25, max: 38 };
            }
            // Very far - top of screen
            else if (distance > ceiling) {
                return { min: 8, max: 20 };
            }
            // Default
            return { min: 25, max: 40 };
        },

        findNonOverlappingPosition(topRange, placedPositions, index) {
            const maxAttempts = 50;
            let attempts = 0;
            let position;

            // Distribute horizontally based on index to spread asteroids out
            const totalAsteroids = this.bodyCollection.length;
            const segmentWidth = 70 / Math.max(totalAsteroids, 1);
            const baseRight = 10 + (index * segmentWidth);
            const rightMin = Math.max(10, baseRight - 5);
            const rightMax = Math.min(80, baseRight + segmentWidth);

            while (attempts < maxAttempts) {
                position = {
                    right: this.getRandomInt(rightMin, rightMax),
                    top: this.getRandomInt(topRange.min, topRange.max),
                };

                if (!this.overlapsWithAny(position, placedPositions)) {
                    return position;
                }
                attempts++;
            }

            // If we couldn't find a non-overlapping position, offset from last position
            if (placedPositions.length > 0) {
                const lastPos = placedPositions[placedPositions.length - 1];
                position = {
                    right: (lastPos.right + this.minDistance) % 70 + 10,
                    top: topRange.min + ((lastPos.top + 8) % (topRange.max - topRange.min)),
                };
            }

            return position;
        },

        overlapsWithAny(position, placedPositions) {
            for (const placed of placedPositions) {
                const distance = Math.sqrt(
                    Math.pow(position.right - placed.right, 2) +
                    Math.pow(position.top - placed.top, 2)
                );
                if (distance < this.minDistance) {
                    return true;
                }
            }
            return false;
        },

        getPosition(body, index) {
            if (this.calculatedPositions[body.neo_reference_id]) {
                return this.calculatedPositions[body.neo_reference_id];
            }
            // Fallback if position not calculated yet
            return { right: 10 + (index * 8) % 70, top: 30 };
        },

        getRandomInt(min, max) {
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },
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
</style>