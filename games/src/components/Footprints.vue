<script>
    export default {
        name: 'Footprints',
        props: ['imgSrc'],
    }
</script>

<template>
    <div>
        <img :src="imgSrc" alt="hero" class="footprints-image" width="60" height="100"/>
    </div>
</template>

<style scoped lang="scss">
    .footprints-image {
        display: block;
        width: 100%;
        height: auto;
    }
</style>
