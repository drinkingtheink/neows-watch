<template>
    <div class="heavenly-body" @click="bodySelect(body)">
        <span class="label">{{ body.name }}</span>
        <button class="disc" />
    </div>
</template>

<script>
import { EventBus } from '../EventBus';

export default {
    name: 'Body',
    props: {
        body: Object,
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
    transform: translateX(100%);
    opacity: 0;
  }

  to {
    transform: translateX(0%);
    opacity: 1;
  }
}

@mixin bodyAppear {
    animation: appear 1s;
    animation-direction: forward;
    animation-fill-mode: both;
}

.heavenly-body {
    @include bodyAppear;
    position: absolute;
    display: flex;
    flex-direction: column;
    align-content: center;
    transition: all .2s;

    &:hover {
        cursor: pointer;
        opacity: 1;

        .disc, .label {
            opacity: 1;
        }
    }

    .disc, .label {
        opacity: 0.6;
        transition: all .2s;

        &:hover {
            cursor: pointer;
        }
    }
}

$bodyDim: 20px;

.disc {
    border-radius: 50%;
    height: $bodyDim;
    width: $bodyDim;
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
