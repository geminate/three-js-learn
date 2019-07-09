<!-- .mtl 和 .obj 文件的加载、静态2D标签的添加 -->
<template>
    <div>
        <div ref="container" class="container"></div>
    </div>
</template>

<script>
    import * as THREE from 'three';
    import {GLTFLoader} from 'three/examples/jsm/loaders/GLTFLoader';
    import OrbitControls from 'three-orbit-controls';

    export default {
        name: 'App',
        data() {
            return {
                container: null,
                scene: '',
                light: '',
                camera: '',
                controls: '',
                renderer: ''
            }
        },
        methods: {
            init() {
                this.initScene();
                this.initLight();
                this.initCamera();
                this.initControls();
                this.initObj();
                this.initRenderer();
            },

            // 初始化画布
            initScene() {
                this.scene = new THREE.Scene();
                this.container = this.$refs.container;
            },

            // 初始化模型渲染
            initRenderer() {
                this.renderer = new THREE.WebGLRenderer({alpha: true});
                this.renderer.setSize(this.container.clientWidth, this.container.clientHeight);
                this.container.appendChild(this.renderer.domElement);
            },

            // 初始化光源
            initLight() {
                this.scene.add(new THREE.AmbientLight('#FFFFFF')); // 环境光
                this.light = new THREE.DirectionalLight('#DFEBFF', 1.9);
                this.light.position.set(0, 20, 0);
                this.light.position.multiplyScalar(0.3);
                this.scene.add(this.light);
            },

            // 初始化相机
            initCamera() {
                this.camera = new THREE.PerspectiveCamera(60, this.container.clientWidth / this.container.clientHeight, 1, 1000);
                this.camera.position.set(0, 20, 100);
            },

            // 初始化控制器
            initControls() {
                const orb = OrbitControls(THREE);
                this.controls = new orb(this.camera, this.container);
                this.controls.target.set(0, 0, 0);
//                this.controls.autoRotate = true;
//                this.controls.autoRotateSpeed = -6;
                this.controls.update();
            },

            // 初始化模型
            initObj() {
                let loader = new GLTFLoader();
                loader.load('static/gltf/scene.gltf', (gltf) => {
                    this.scene.add(gltf.scene);
                });
            },

            // 渲染
            animate() {
                requestAnimationFrame(this.animate);
                this.controls.update();
                this.renderer.render(this.scene, this.camera);
            },
        },
        mounted() {
            this.init();
            this.animate();
        }
    }
</script>

<style scoped>
    .container {
        width: 500px;
        height: 500px;
        border: 1px solid black;
    }

    .label-container {
        display: none;
    }

    .label {
        font-size: 12px;
    }
</style>
