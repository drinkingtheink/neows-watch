<template>
    <div class="object-display-stage">
        <div 
            v-for="body in objectCollection" 
            :key="body.neo_reference_id"
            class="heavenly-body"
        >
            <p>{{ body.name }}</p>
            <span class="disc" />
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
.object-display-stage {
    position: relative;
}

$bodyDim: 20px;

.disc {
    border-radius: 50%;
    height: $bodyDim;
    width: $bodyDim;
    background-color: white;
    display: inline-block;
    position: absolute;
}
</style>