<template>
    <button class="heavenly-body" :class="{ 'potential-threat': potentialThreat }" @click="bodySelect(body)">
        <span class="label">{{ body.name }}</span>
        <span 
            class="disc"
            :style="{ 'height': `${relativeSize}px`, 'width': `${relativeSize}px` }" />
    </button>
</template>

<script>
import { EventBus } from '../EventBus';

export default {
    name: 'Body',
    props: {
        body: Object,
    },
    computed: {
        size() {
            return this.body.estimated_diameter.feet;
        },
        relativeSize() {
            let newFig = parseInt(this.size.estimated_diameter_max, 10).toFixed(2) / 10;

            if (newFig < 20) {
                newFig = 20;
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

.heavenly-body {
    @include bodyAppear;
    position: absolute;
    display: flex;
    flex-direction: column;
    align-content: center;
    transition: all .2s;
    border: none;
    background: transparent;

    &:hover {
        cursor: pointer;
        opacity: 1;

        .disc, .label {
            opacity: 1;
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
            border-left: 4px $red;
        }

        .disc {
            background-color: $lightred;
        }
    }
}

.disc {
    border-radius: 50%;
    background-color: white;
    display: inline-block;
    margin: 0 auto;
}

.label {
    background-color: rgba(black, 0.5);
    color: $yellow;
    padding: 4px;
    font-size: .75rem;
    margin-bottom: 5px;
}
</style>
