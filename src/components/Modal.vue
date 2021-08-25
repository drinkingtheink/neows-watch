<template>
    <transition name="modal-fade">
        <div class="modal-backdrop" @click="close">
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
                    <h4>Size: (estimated diameter)</h4>
                    <div class="body-section">
                        <section>
                            <p>MIN: {{ content.estimated_diameter.feet.estimated_diameter_min }}</p>
                            <p>MAX: {{ content.estimated_diameter.feet.estimated_diameter_min }}</p>
                        </section>
                        <section>
                            <p>MIN: {{ content.estimated_diameter.kilometers.estimated_diameter_min }}</p>
                            <p>MAX: {{ content.estimated_diameter.kilometers.estimated_diameter_min }}</p>
                        </section>
                        <section>
                            <p>MIN: {{ content.estimated_diameter.miles.estimated_diameter_min }}</p>
                            <p>MAX: {{ content.estimated_diameter.miles.estimated_diameter_min }}</p>
                        </section>
                    </div>

                    <h4>Speed</h4>
                    <div class="body-section">
                        
                    </div>

                    <h4>Approach</h4>
                    <div class="body-section">
                        
                    </div>
                </section>

                <footer class="modal-footer">
        
                    <button
                        type="button"
                        class="btn-green"
                        @click="close"
                    >
                        Close Modal
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

    .body-section {
        display: flex;

        section {
            width: 30%;
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

.btn-green {
    color: white;
    background: $black;
    border: 1px solid $yellow;
    border-radius: 2px;
}

.modal-fade-enter,
.modal-fade-leave-to {
    opacity: 0;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
    transition: opacity .5s ease;
}
</style>