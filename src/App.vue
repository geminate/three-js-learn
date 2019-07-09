<template>
    <div class="container">
        <obj-load class="item"></obj-load>
        <gltf-load class="item"></gltf-load>
    </div>
</template>

<script>
    import objLoad from './objLoad';
    import gltfLoad from './gltfLoad';

    export default {
        name: 'App',
        components: {objLoad, gltfLoad}
    }
</script>

<style scoped>
    .container {
        display: flex;
    }

    .item {
        margin: 5px;
    }
</style>
