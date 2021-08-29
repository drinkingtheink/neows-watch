<template>
    <transition name="modal-fade">
        <div class="no-bodies-found">
            <h2>No Near-Earth Objects Found for Your Search</h2>
            <p>You're on your own on this one. Keep your eyes to the sky!</p>

            <section class="actions">
                <button @click="goToTodaySearch">Search for Today</button>
                <button @click="goToYearInput">Change Search Terms</button>
            </section>
        </div>
    </transition>
</template>

<script>
import { EventBus } from '../EventBus';

export default {
    name: 'NoBodiesFound',
    methods: {
        goToTodaySearch() {
            EventBus.$emit('search-today');
        },
        goToYearInput() {
            EventBus.$emit('to-year-input');
        },
    }
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';

.no-bodies-found {
    width: 50rem;
    position: absolute;
    margin: auto;
    left: 0; right: 0;
    top: -40%; bottom: 0;
    padding: 2rem;
    background-color: rgba(black, 0.7);
    color: $yellow;
    border-radius: 10px;
    max-height: 10rem;
    box-shadow: 0px 5px 5px 0 rgba(black, 0.5);

    button {
        margin-right: .5rem;
    }

    p {
        color: white;
    }
}
</style>
