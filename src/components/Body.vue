<template>
    <button class="heavenly-body" :class="{ 'potential-threat': potentialThreat }" @click="bodySelect(body)">
        <div class="label">
            <div>
                <strong>{{ body.name }}</strong>
                <div class="count-stage">
                    <ICountUp
                        :delay="delay"
                        :endVal="parseInt(speed.miles_per_hour, 10)"
                    /> 
                    <span class="mph">MPH</span>
                </div>
            </div>
        </div>
        <span
            class="disc"
            :style="{ 'height': `${relativeSize}px`, 'width': `${relativeSize}px`, 'animation-delay': `0.${bodyIndex}0s` }">
            <span class="trail" :style="trailStyle"></span>
            <Asteroid1 v-if="astToDisplay === 'Asteroid1'" :relativeSize="relativeSize" :threat="potentialThreat" />
            <Asteroid2 v-if="astToDisplay === 'Asteroid2'" :relativeSize="relativeSize" :threat="potentialThreat" />
            <Asteroid3 v-if="astToDisplay === 'Asteroid3'" :relativeSize="relativeSize" :threat="potentialThreat" />
        </span>
    </button>
</template>

<script>
import { EventBus } from '../EventBus';
import ICountUp from 'vue-countup-v2';

export default {
    name: 'Body',
    components: {
        'Asteroid1': () => import('./Asteroid1'),
        'Asteroid2': () => import('./Asteroid2'),
        'Asteroid3': () => import('./Asteroid3'),
        ICountUp,
    },
    props: {
        body: Object,
        bodyIndex: Number,
    },
    data() {
        return {
            astToDisplay: null,
            delay: 1000,
        }
    },
    computed: {
        size() {
            return this.body.estimated_diameter.feet;
        },
        relativeSize() {
            let newFig = parseInt(this.size.estimated_diameter_max, 10).toFixed(2) / 10;
            const floor = 20;
            const ceiling = 350;

            if (newFig < floor) {
                newFig = floor;
            }

            if (newFig > ceiling) {
                newFig = ceiling;
            }
            return newFig;
        },
        potentialThreat() {
            return this.body.is_potentially_hazardous_asteroid;
        },
        speed() {
            return this.body.close_approach_data[0].relative_velocity;
        },
        trailStyle() {
            // Calculate trail length based on speed (typical range: 10,000 - 90,000 mph)
            const speedMph = parseInt(this.speed.miles_per_hour, 10);
            const isHazardous = this.potentialThreat;

            // Hazardous asteroids get larger trails
            const minTrail = isHazardous ? 60 : 30;
            const maxTrail = isHazardous ? 250 : 150;
            const baseOpacity = isHazardous ? 0.5 : 0.3;
            const maxOpacity = isHazardous ? 0.85 : 0.7;

            // Normalize speed to a 0-1 range (assuming 10k-90k mph range)
            const normalizedSpeed = Math.min(Math.max((speedMph - 10000) / 80000, 0), 1);
            const trailLength = minTrail + (normalizedSpeed * (maxTrail - minTrail));
            const trailHeight = isHazardous ? '60%' : '40%';

            return {
                width: `${trailLength}px`,
                height: trailHeight,
                opacity: baseOpacity + (normalizedSpeed * (maxOpacity - baseOpacity))
            };
        }
    },
    mounted() {
        let randomBody = this.getRandomAsteroid();
        this.astToDisplay = randomBody;
    },
    methods: {
        bodySelect(body) {
            EventBus.$emit('body-selected', body);
        },
        getRandomAsteroid() {
            let bodies = ['Asteroid1', 'Asteroid2', 'Asteroid3'];
            let randomBody = bodies[Math.floor(Math.random() * bodies.length)];
            return randomBody;
        }
    }
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';
@import '../styles/typography';

@keyframes appear {
  from {
    transform: translateX(2000%);
    opacity: 0;
  }

  to {
    transform: translateX(0%);
    opacity: 1;
  }
}

@mixin bodyAppear {
    animation: appear 1.5s;
    animation-direction: forward;
    animation-fill-mode: both;
    animation-timing-function: ease-in;
}

@keyframes discSwell {
    from {
        transform: scale(1.05);
    }

    to {
        transform: scale(1);
    }
}

@keyframes discThreatSwell {
    from {
        transform: scale(1.05);
        background-color: rgba($lightred, 0.4);
    }

    to {
        transform: scale(1);
        background-color: rgba($lightred, 0.7);
    }
}

@keyframes asteroidExit {
    0% {
        transform: translateX(0) scale(1);
        opacity: 1;
    }
    100% {
        transform: translateX(-400px) scale(0.2);
        opacity: 0;
    }
}

.heavenly-body {
    @include bodyAppear;
    position: absolute;
    display: flex;
    flex-direction: column;
    align-content: center;
    transition: all .2s;
    border: none;
    background: transparent;
    transition: all .4s;
    border-radius: 10px;

    // Exit animation when leaving the DOM
    &.asteroid-leave-active {
        animation: asteroidExit 0.7s ease-in forwards;
        pointer-events: none;
    }

    &:hover {
        cursor: pointer;
        opacity: 1;

        .disc, .label {
            opacity: 1;
        }
    }

    .disc {
        border-radius: 50%;
        background: radial-gradient(circle, rgba(255,255,255,0) 38%, rgba($yellow,1) 100%);
        display: inline-block;
        margin: 0 auto;
        animation: discSwell 1s infinite;
        animation-direction: alternate;
        display: flex;
        align-content: center;
        justify-content: center;
        position: relative;
    }

    .trail {
        position: absolute;
        left: 20%;
        top: 50%;
        transform: translateY(-50%);
        height: 55%;
        background: linear-gradient(to right, rgba($yellow, 0.9) 0%, rgba($yellow, 0.7) 15%, rgba($yellow, 0.3) 40%, rgba($yellow, 0) 100%);
        border-radius: 50% 40% 40% 50% / 50% 50% 50% 50%;
        pointer-events: none;
        z-index: -1;
        animation: trailPulse 2s ease-in-out infinite alternate;
    }

    @keyframes trailPulse {
        0% {
            opacity: 0.4;
            transform: translateY(-50%) scaleX(0.95);
        }
        100% {
            opacity: 0.7;
            transform: translateY(-50%) scaleX(1.05);
        }
    }

    .label {
        background-color: rgba(black, 0.5);
        color: $yellow;
        padding: 4px 4px 4px 10px;
        font-size: .75rem;
        margin: 0 0 5px 0;
        border-left: 2px solid $yellow;
        text-align: left;

        p {
            margin: 0;
            padding: 0;
        }

        strong {
            font-family: $headerFont !important;
            margin-right: .25rem;
        }

        .count-stage {
            width: 3rem;
        }

        .mph {
            font-size: 75%;
        }
    }

    .disc, .label {
        opacity: 0.75;
        transition: all .2s;

        &:hover {
            cursor: pointer;
        }
    }

    &.potential-threat {
        .label {
            background-color: rgba($red, 0.5);
            color: white;
            border-color: white;
        }

        .disc {
            background: radial-gradient(circle, rgba(255,255,255,0) 55%, rgba($lightred,1) 100%);
            animation: discThreatSwell 1s infinite;
            animation-direction: alternate;
        }

        .trail {
            background: linear-gradient(to right, rgba($lightred, 0.95) 0%, rgba($lightred, 0.7) 15%, rgba($lightred, 0.3) 40%, rgba($lightred, 0) 100%);
        }
    }

    &:focus {
        opacity: 1;
        background: rgba(white, 0.1);
        outline: none;
        padding: 10px;
    }
}
</style>
