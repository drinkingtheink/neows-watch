<template>
    <button class="heavenly-body" :class="{ 'potential-threat': potentialThreat }" @click="bodySelect(body)">
        <span class="label">{{ body.name }}</span>
        <span 
            class="disc"
            :style="{ 'height': `${relativeSize}px`, 'width': `${relativeSize}px`, 'animation-delay': `0.${bodyIndex}0s` }">
            <Asteroid1 :relativeSize="relativeSize" :threat="potentialThreat" />
        </span>
    </button>
</template>

<script>
import { EventBus } from '../EventBus';
import Asteroid1 from './Asteroid1';

export default {
    name: 'Body',
    components: {
        Asteroid1,
    },
    props: {
        body: Object,
        bodyIndex: Number,
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
    },
    methods: {
        bodySelect(body) {
            EventBus.$emit('body-selected', body);
        }
    }
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';

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
    }

    .label {
        background-color: rgba(black, 0.5);
        color: $yellow;
        padding: 4px;
        font-size: .75rem;
        margin-bottom: 5px;
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
            border-left: 4px $red;
        }

        .disc {
            background: radial-gradient(circle, rgba(255,255,255,0) 55%, rgba($lightred,1) 100%);
            animation: discThreatSwell 1s infinite;
            animation-direction: alternate;
        }
    }
}
</style>
