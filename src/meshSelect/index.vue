<!-- .mtl 和 .obj 文件的加载、静态2D标签的添加 -->
<template>
    <div>
        <div ref="container" class="container" @mousemove="onMouseMove"></div>
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
                container: null, // 画布容器
                scene: '', // 场景对象
                light: '', // 光线对象
                camera: '', // 相机对象
                controls: '', // 控制器对象
                renderer: '', // 渲染对象
                mesh: [],
                clock: new THREE.Clock(), // 时钟对象
                mouse: new THREE.Vector2(), // 鼠标指针对象
                rayCaster: new THREE.Raycaster(), // 鼠标指针射线对象
                hightLight: '' // 已经高亮的对象
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
                this.scene.background = new THREE.Color('#a9dfef');
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
                this.light = new THREE.PointLight('#FFFFFF', 1);
                this.light.position.set(40, 40, 40);
                this.scene.add(this.light);
            },

            // 初始化相机
            initCamera() {
                this.camera = new THREE.PerspectiveCamera(60, this.container.clientWidth / this.container.clientHeight, 1, 9000);
                this.camera.position.set(0, 20, 100);
            },

            // 初始化控制器
            initControls() {
                const orb = OrbitControls(THREE);
                this.controls = new orb(this.camera, this.container);
                this.controls.target.set(0, 0, 0);
                this.controls.autoRotate = true;
                this.controls.autoRotateSpeed = -6;
                this.controls.update();
            },

            // 初始化模型
            initObj() {
                let geometry = new THREE.BoxBufferGeometry(10, 10, 10);
                for (let i = 0; i < 20; i++) {
                    let object = new THREE.Mesh(geometry, new THREE.MeshLambertMaterial({color: Math.random() * 0xffffff}));
                    object.position.x = Math.random() * 60 - 30;
                    object.position.y = Math.random() * 60 - 30;
                    object.position.z = Math.random() * 60 - 30;
                    object.rotation.x = Math.random() * 2 * Math.PI;
                    object.rotation.y = Math.random() * 2 * Math.PI;
                    object.rotation.z = Math.random() * 2 * Math.PI;
                    object.scale.x = Math.random() + 0.5;
                    object.scale.y = Math.random() + 0.5;
                    object.scale.z = Math.random() + 0.5;
                    this.scene.add(object);
                }
            },

            // 鼠标指针移动事件
            onMouseMove(event) {
                this.mouse.x = ( event.offsetX / this.container.clientWidth ) * 2 - 1;
                this.mouse.y = -( event.offsetY / this.container.clientHeight ) * 2 + 1;
            },

            // 渲染
            animate() {
                requestAnimationFrame(this.animate);
                this.controls && this.controls.update();
                this.animateRay();
                this.renderer.render(this.scene, this.camera);
            },

            // 鼠标指向渲染
            animateRay() {
                this.rayCaster.setFromCamera(this.mouse, this.camera);
                const intersects = this.rayCaster.intersectObjects(this.scene.children);
                if (intersects.length > 0 && (this.hightLight != intersects[0].object)) {
                    this.hightLight && this.hightLight.material.color.setHex(this.hightLight.pastColor);
                    this.hightLight = intersects[0].object;
                    intersects[0].object.pastColor = intersects[0].object.material.color.getHex();
                    intersects[0].object.material.color.set(0xff0000);
                }
            }
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
</style>
