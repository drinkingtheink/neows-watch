<template>
    <transition name="modal-fade">
        <div class="modal-backdrop">
            <div class="modal">
                <header class="modal-header">
                    <div v-if="content">
                        <h2 class="neo-name">
                            NEO: {{ content.name }}
                            <a :href="content.nasa_jpl_url" target="_blank">More</a>
                        </h2>
                        <p class="id">ID: {{ content.neo_reference_id }}</p>
                    </div>
                    <button
                        type="button"
                        class="btn-close"
                        @click="close"
                        >
                        x
                    </button>
                </header>

                <section class="modal-body" v-if="content">
                    <div class="body-section">
                        <section>
                            <h6>APPROACHING</h6>
                            <p>{{ approachData.close_approach_date_full }}</p>
                        </section>
                        <section>
                            <h6>ABSOLUTE MAGNITUDE</h6>
                            <p>h = {{ content.absolute_magnitude_h }}</p>
                        </section>
                        <section>
                            <h6>THREAT TO EARTH</h6>
                            <p v-if="content.is_potentially_hazardous_asteroid">POTENTIALLY HAZARDOUS</p>
                            <p v-else>NO THREAT</p>
                        </section>
                    </div>

                    <h4>Size (estimated diameter):</h4>
                    <div class="body-section">
                        <section>
                            <h6>FEET</h6>
                            <p><small>MIN:</small> {{ content.estimated_diameter.feet.estimated_diameter_min }}</p>
                            <p><small>MAX:</small> {{ content.estimated_diameter.feet.estimated_diameter_max }}</p>
                        </section>
                        <section>
                            <h6>Meters</h6>
                            <p><small>MIN:</small> {{ content.estimated_diameter.meters.estimated_diameter_min }}</p>
                            <p><small>MAX:</small> {{ content.estimated_diameter.meters.estimated_diameter_max }}</p>
                        </section>
                        <section>
                            <h6>MILES</h6>
                            <p><small>MIN:</small> {{ content.estimated_diameter.miles.estimated_diameter_min }}</p>
                            <p><small>MAX:</small> {{ content.estimated_diameter.miles.estimated_diameter_max }}</p>
                        </section>
                    </div>

                    <h4>Speed:</h4>
                    <div class="body-section">
                        <section>
                            <h6>MPH</h6>
                            <p>{{ speed.miles_per_hour }}</p>
                        </section>
                        <section>
                            <h6>kM/h</h6>
                            <p>{{ speed.kilometers_per_hour }}</p>
                        </section>
                        <section>
                            <h6>kM/s</h6>
                            <p>{{ speed.kilometers_per_second }}</p>
                        </section>
                    </div>

                    <h4>Closest Distance to Earth (perigee):</h4>
                    <div class="body-section">
                        <section>
                            <h6>AU</h6>
                            <p>{{ missDistance.astronomical }}</p>
                        </section>
                        <section>
                            <h6>kM</h6>
                            <p>{{ missDistance.kilometers }}</p>
                        </section>
                        <section>
                            <h6>Lunar</h6>
                            <p>{{ missDistance.lunar }}</p>
                        </section>
                        <section>
                            <h6>Miles</h6>
                            <p>{{ missDistance.miles }}</p>
                        </section>
                    </div>
                </section>

                <footer class="modal-footer">
        
                    <button @click="close">
                        Back to the Sky
                    </button>
                </footer>
            </div>
        </div>
    </transition>
</template>

<script>
  export default {
    name: 'Modal',
    props: {
        content: Object,
    },
    computed: {
        approachData: function() {
            return this.content.close_approach_data[0];
        },
        speed: function() {
            return this.approachData.relative_velocity;
        },
        missDistance: function() {
            return this.approachData.miss_distance;
        },
    },
    methods: {
      close() {
        this.$emit('close');
      },
    },
  };
</script>

<style lang="scss" scoped>
@import '../styles/palette';

.modal-backdrop {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal {
    background:white;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
    min-width: 50rem;

    h1, h2, h3, h5, h6 {
        margin-top: 0;
    }

    h4 {
        margin-top: 2rem;
    }
}

.neo-name, .id {
    margin: 0;
    padding: 0;
}

h3.id {
    color: $black;
}

.modal-header,
.modal-footer {
    padding: 15px;
    display: flex;
    background-color: $grey;
}

.modal-header {
    position: relative;
    border-bottom: 1px solid #eeeeee;
    justify-content: space-between;
}

.modal-footer {
    border-top: 1px solid #eeeeee;
    flex-direction: column;
    justify-content: flex-end;
}

.modal-body {
    position: relative;
    padding: 20px 10px;
    height: 28rem;
    overflow-x: auto;

    .body-section {
        display: flex;

        section {
            width: 33%;
        }
    }
}

.btn-close {
    position: absolute;
    top: 0;
    right: 0;
    border: none;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
    background: transparent;
}

button {
    color: $black;
    background: $yellow;
    border: 2px solid $yellow;
    border-radius: 2px;
    padding: 1rem 2rem;
    text-transform: uppercase;
    font-size: 120%;
    transition: all .2s;
    border-radius: 20px;

    &:hover {
        cursor: pointer;
        background: black;
        color: $yellow;
        border-color: $yellow;
    }
}

.modal-fade-enter,
.modal-fade-leave-to {
    opacity: 0;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
    transition: opacity .5s ease;
}

small {
    font-size: 50%;
    font-weight: bold;
}
</style>