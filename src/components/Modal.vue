<template>
    <transition name="modal-fade">
        <div class="modal-backdrop">
            <div class="modal">
                <header class="modal-header">
                    <div v-if="content">
                        <h2 class="neo-name">{{ content.name }}</h2>
                        <p class="neo-id"><span>ID: {{ content.neo_reference_id }}</span> <a class="more-info" :href="content.nasa_jpl_url" target="_blank">More Info</a></p>
                    </div>
                    <button
                        type="button"
                        class="btn-close"
                        @click="close"
                        >
                        x
                    </button>
                </header>
                <div class="asteroid-showcase" :class="{ 'is-threat': content && content.is_potentially_hazardous_asteroid }">
                    <div class="stars"></div>
                    <div class="asteroid-closeup">
                        <span class="closeup-trail" :style="trailStyle"></span>
                        <div class="asteroid-wrapper">
                            <Asteroid1 v-if="asteroidType === 'Asteroid1'" :relativeSize="180" :threat="content && content.is_potentially_hazardous_asteroid" />
                            <Asteroid2 v-if="asteroidType === 'Asteroid2'" :relativeSize="180" :threat="content && content.is_potentially_hazardous_asteroid" />
                            <Asteroid3 v-if="asteroidType === 'Asteroid3'" :relativeSize="180" :threat="content && content.is_potentially_hazardous_asteroid" />
                        </div>
                    </div>
                </div>

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
                            <p class="threat threat-detected" v-if="content.is_potentially_hazardous_asteroid">POTENTIALLY HAZARDOUS</p>
                            <p class="threat no-threat" v-else>NO THREAT</p>
                        </section>
                    </div>

                    <h4>Size (estimated diameter):</h4>
                    <div class="body-section">
                        <section>
                            <h6>FEET</h6>
                            <p><small>MIN:</small> {{ formatNumber(content.estimated_diameter.feet.estimated_diameter_min) }}</p>
                            <p><small>MAX:</small> {{ formatNumber(content.estimated_diameter.feet.estimated_diameter_max) }}</p>
                        </section>
                        <section>
                            <h6>Meters</h6>
                            <p><small>MIN:</small> {{ formatNumber(content.estimated_diameter.meters.estimated_diameter_min) }}</p>
                            <p><small>MAX:</small> {{ formatNumber(content.estimated_diameter.meters.estimated_diameter_max) }}</p>
                        </section>
                        <section>
                            <h6>MILES</h6>
                            <p><small>MIN:</small> {{ formatNumber(content.estimated_diameter.miles.estimated_diameter_min) }}</p>
                            <p><small>MAX:</small> {{ formatNumber(content.estimated_diameter.miles.estimated_diameter_max) }}</p>
                        </section>
                    </div>

                    <h4>Speed (relative velocity):</h4>
                    <div class="body-section">
                        <section>
                            <h6>MPH</h6>
                            <p>{{ formatNumber(speed.miles_per_hour) }}</p>
                        </section>
                        <section>
                            <h6>kM/h</h6>
                            <p>{{ formatNumber(speed.kilometers_per_hour) }}</p>
                        </section>
                        <section>
                            <h6>kM/s</h6>
                            <p>{{ formatNumber(speed.kilometers_per_second) }}</p>
                        </section>
                    </div>

                    <h4>Closest Distance to Earth (perigee):</h4>
                    <div class="body-section">
                        <section>
                            <h6>kM</h6>
                            <p>{{ formatNumber(missDistance.kilometers) }}</p>
                        </section>
                        <section>
                            <h6>Lunar</h6>
                            <p>{{ formatNumber(missDistance.lunar) }}</p>
                        </section>
                        <section>
                            <h6>Miles</h6>
                            <p>{{ formatNumber(missDistance.miles) }}</p>
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
components: {
    'Asteroid1': () => import('./Asteroid1'),
    'Asteroid2': () => import('./Asteroid2'),
    'Asteroid3': () => import('./Asteroid3'),
},
props: {
    content: Object,
},
data() {
    return {
        asteroidType: null,
    };
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
    trailStyle: function() {
        if (!this.content) return {};
        const speedMph = parseInt(this.speed.miles_per_hour, 10);
        const isHazardous = this.content.is_potentially_hazardous_asteroid;

        const minTrail = isHazardous ? 200 : 150;
        const maxTrail = isHazardous ? 450 : 350;

        const normalizedSpeed = Math.min(Math.max((speedMph - 10000) / 80000, 0), 1);
        const trailLength = minTrail + (normalizedSpeed * (maxTrail - minTrail));

        return {
            width: `${trailLength}px`,
        };
    },
},
mounted() {
    const bodies = ['Asteroid1', 'Asteroid2', 'Asteroid3'];
    this.asteroidType = bodies[Math.floor(Math.random() * bodies.length)];
},
methods: {
    close() {
        this.$emit('close');
    },
    formatNumber(num) {
        let formattedNum;

        if (num < 1) {
            formattedNum = '< 1'
        } else {
            formattedNum = Number.parseFloat(num).toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        }
        return formattedNum;
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
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.asteroid-showcase {
    position: relative;
    height: 180px;
    flex-shrink: 0;
    background: linear-gradient(135deg, #0d0d2b 0%, #1a1a4e 50%, #0d0d2b 100%);
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;

    .stars {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-image:
            radial-gradient(2px 2px at 20px 30px, white, transparent),
            radial-gradient(2px 2px at 40px 70px, rgba(255,255,255,0.8), transparent),
            radial-gradient(1px 1px at 90px 40px, white, transparent),
            radial-gradient(2px 2px at 130px 80px, rgba(255,255,255,0.6), transparent),
            radial-gradient(1px 1px at 160px 20px, white, transparent),
            radial-gradient(2px 2px at 200px 60px, rgba(255,255,255,0.7), transparent),
            radial-gradient(1px 1px at 250px 90px, white, transparent),
            radial-gradient(2px 2px at 300px 30px, rgba(255,255,255,0.8), transparent),
            radial-gradient(1px 1px at 350px 70px, white, transparent),
            radial-gradient(2px 2px at 400px 50px, rgba(255,255,255,0.6), transparent),
            radial-gradient(1px 1px at 450px 85px, white, transparent),
            radial-gradient(2px 2px at 500px 25px, rgba(255,255,255,0.7), transparent),
            radial-gradient(1px 1px at 550px 65px, white, transparent),
            radial-gradient(2px 2px at 600px 45px, rgba(255,255,255,0.8), transparent),
            radial-gradient(1px 1px at 650px 15px, white, transparent),
            radial-gradient(2px 2px at 700px 75px, rgba(255,255,255,0.6), transparent);
        animation: starScroll 3s linear infinite;
    }

    @keyframes starScroll {
        from {
            transform: translateX(0);
        }
        to {
            transform: translateX(-100px);
        }
    }

    .asteroid-closeup {
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
        animation: hurtleBob 2s ease-in-out infinite;
    }

    @keyframes hurtleBob {
        0%, 100% {
            transform: translateY(0) translateX(0);
        }
        25% {
            transform: translateY(-5px) translateX(3px);
        }
        75% {
            transform: translateY(5px) translateX(-3px);
        }
    }

    .asteroid-wrapper {
        position: relative;
        z-index: 2;
        filter: drop-shadow(0 0 20px rgba($yellow, 0.5));
    }

    .closeup-trail {
        position: absolute;
        left: 25%;
        top: 50%;
        transform: translateY(-50%);
        height: 65%;
        background: linear-gradient(to right, rgba($yellow, 0.95) 0%, rgba($yellow, 0.7) 15%, rgba($yellow, 0.3) 40%, rgba($yellow, 0) 100%);
        border-radius: 50% 40% 40% 50% / 50% 50% 50% 50%;
        z-index: 1;
        animation: trailPulse 1.5s ease-in-out infinite alternate;
    }

    @keyframes trailPulse {
        0% {
            opacity: 0.6;
            transform: translateY(-50%) scaleX(0.95);
        }
        100% {
            opacity: 1;
            transform: translateY(-50%) scaleX(1.05);
        }
    }

    &.is-threat {
        background: linear-gradient(135deg, #2b0d0d 0%, #4e1a1a 50%, #2b0d0d 100%);

        .asteroid-wrapper {
            filter: drop-shadow(0 0 25px rgba($lightred, 0.7));
        }

        .closeup-trail {
            height: 80%;
            background: linear-gradient(to right, rgba($lightred, 0.98) 0%, rgba($lightred, 0.75) 15%, rgba($lightred, 0.4) 40%, rgba($lightred, 0) 100%);
        }
    }
}

.modal {
    background:white;
    box-shadow: 2px 2px 20px 1px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    min-width: 50rem;
    max-height: 90vh;
    z-index: 100;
    border-radius: 20px;

    h1, h2, h3, h5, h6 {
        margin-top: 0;
        text-transform: uppercase;
    }

    h4 {
        margin-top: 2rem;
    }
}

.neo-name, .id {
    margin: 0;
    padding: 0;
    font-size: 250%;
}

p.neo-id {
    color: $black;
    font-size: 150%;
    padding: 0;
    margin: 0.25rem 0 0 0;
    display: flex;

    span {
        opacity: 0.6;
    }
}

.modal-header,
.modal-footer {
    padding: 15px;
    display: flex;
    flex-shrink: 0;
    background-color: $grey;
}

.modal-header {
    position: relative;
    border-bottom: 1px solid #eeeeee;
    justify-content: space-between;
    border-radius: 20px 20px 0 0;
}

a.more-info {
    text-decoration: none;
    font-size: 50%;
    padding: 5px;
    background-color: $black;
    color: white;
    border-radius: 5px;
    transition: all .2s;
    border: 2px solid white;
    margin-left: .5rem;

    &:hover {
        background-color: black;
        border-color: $yellow;
        color: $yellow;
    }
}

.modal-footer {
    border-top: 1px solid #eeeeee;
    flex-direction: column;
    justify-content: flex-end;
}

.modal-body {
    position: relative;
    padding: 1rem 2rem;
    flex: 1;
    overflow-y: auto;

    .body-section {
        display: flex;
        padding-bottom: 1rem;
        border-bottom: 1px solid rgba(black, 0.1);

        p {
            font-size: 150%;
        }

        section {
            width: 33%;
        }
    }
}

.btn-close {
    position: absolute;
    top: .5rem;
    right: 1rem;
    border: none;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
    background: transparent;
}

small {
    font-size: 50%;
    font-weight: bold;
}

.threat {
    padding: 10px;
    text-align: center;
    border-radius: 10px;
    margin-top: -10px;
}

.no-threat {
    background-color: $lightgreen;
    color: $green;
}

.threat-detected {
    background-color: $lightred;
    color: $red;
}
</style>