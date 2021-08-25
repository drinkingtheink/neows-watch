<template>
    <div class="object-display-stage">
        <div 
            v-for="body in bodyCollection" 
            :key="body.neo_reference_id"
            class="heavenly-body"
        >
            <span>{{ body.name }}</span>
            <span class="disc" />
        </div>
    </div>
</template>

<script>
export default {
    name: 'ObjectDisplay',
    props: {
        bodyCollection: Array,
        bodyCount: Number
    },
}
</script>

<style lang="scss" scoped>
.object-display-stage {
    position: relative;
}

.heavenly-body {
    position: absolute;
}

$bodyDim: 20px;

.disc {
    border-radius: 50%;
    height: $bodyDim;
    width: $bodyDim;
    background-color: white;
    display: inline-block;
}
</style>