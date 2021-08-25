<template>
    <div class="object-display-stage">
        <div v-for="body in objectCollection" :key="body.neo_reference_id">
            <p>{{ body.name }}</p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ObjectDisplay',
    props: {
        objectCollection: Object
    },
}
</script>

<style lang="scss" scoped>

</style>