<template>
    <div ref="container" class="container">
    </div>
</template>

<script>
    import * as THREE from 'three';
    import {MTLLoader, OBJLoader} from 'three-obj-mtl-loader';
    import OrbitControls from 'three-orbit-controls';
    import {CSS2DRenderer, CSS2DObject} from 'three-css2drender';

    export default {
        name: 'App',
        data() {
            return {
                container: null,
                scene: '',
                light: '',
                camera: '',
                controls: '',
                renderer: '',
                labelRenderer: ''
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
                this.initLabelRenderer();
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

            // 初始化 2D 标签渲染
            initLabelRenderer() {
                this.labelRenderer = new CSS2DRenderer();
                this.labelRenderer.setSize(this.container.clientWidth, this.container.clientHeight);
                this.labelRenderer.domElement.style.position = 'absolute';
                this.labelRenderer.domElement.style.top = 0;
                this.container.appendChild(this.labelRenderer.domElement);
            },

            // 初始化光源
            initLight() {
                this.scene.add(new THREE.AmbientLight('#999999')); // 环境光
                this.light = new THREE.DirectionalLight('#DFEBFF', 0.45);
                this.light.position.set(50, 200, 100);
                this.light.position.multiplyScalar(0.3);
                this.scene.add(this.light);
            },

            // 初始化相机
            initCamera() {
                this.camera = new THREE.PerspectiveCamera(60, this.container.clientWidth / this.container.clientHeight, 10, 1000);
                this.camera.position.set(0, 20, 100);
            },

            // 初始化控制器
            initControls() {
                const orb = OrbitControls(THREE);
                this.controls = new orb(this.camera);
                this.controls.target.set(0, 0, 0);
                this.controls.update();
            },

            // 初始化模型
            initObj() {
                new MTLLoader().setPath('/static/').load('Cerberus.mtl', materials => {
                    materials.preload();
                    new OBJLoader().setMaterials(materials).setPath('/static/').load('Cerberus.obj', obj => {
                        obj.scale.set(50, 50, 50);
                        obj.position.set(50, 0, 0);
                        this.addLabel(obj);
                        this.scene.add(obj);
                    })
                });
            },

            // 初始化标签
            addLabel(mesh) {
                let label = document.createElement('div');
                label.textContent = '一把枪';
                label.style.fontSize = '15px';
                this.biaozhuLabel = new CSS2DObject(label);
                this.biaozhuLabel.position.set(0, 0.18, -0.2);
                mesh.add(this.biaozhuLabel);
            },

            // 渲染
            animate() {
                requestAnimationFrame(this.animate);
                this.controls.update();
                this.renderer.render(this.scene, this.camera);
                this.labelRenderer.render(this.scene, this.camera);
            },
        },
        mounted() {
            this.init();
            this.animate();
        }
    }
</script>

<style>
    .container {
        width: 500px;
        height: 500px;
        border: 1px solid black;
    }
</style>
