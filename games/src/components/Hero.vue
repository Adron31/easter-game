<script>
    export default {
        name: 'Hero',
        props: ['imgSrc'],
    }
</script>

<template>
    <div>
        <img :src="imgSrc" alt="hero" class="hero-image" />
    </div>
</template>

<style scoped lang="scss">
    .hero-image {
        display: block;
        width: 160px;
        height: auto;
    }
</style>
