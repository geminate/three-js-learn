<template>
    <div class="container">
        <obj-load class="item"></obj-load>
        <gltf-load class="item"></gltf-load>
        <mesh-select class="item"></mesh-select>
    </div>
</template>

<script>
    import objLoad from './objLoad';
    import gltfLoad from './gltfLoad';
    import meshSelect from './meshSelect';

    export default {
        name: 'App',
        components: {objLoad, gltfLoad, meshSelect}
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
