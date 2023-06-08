<template>
    <div>
        <div ref="container"></div>
    </div>
</template>
<script lang="ts">
import * as THREE from "three"
import Stats from 'three/examples/jsm/libs/stats.module.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { RoomEnvironment } from 'three/examples/jsm/environments/RoomEnvironment.js';

import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js';
//scene在three里设置configruate为false，
//而vue3监听用到的poxry必须要将configrate设置为true，所以此参数不能放在data里
//切scene.add方法内参数必须通过toRaw进行转化
import { toRaw } from "@vue/reactivity"

const scene = new THREE.Scene();
export default {
    data() {
        return {
            camera: {} as any,
            renderer: {} as any,
            container: {} as HTMLElement,
            mixer: {} as THREE.AnimationMixer,
            clock:new THREE.Clock(),
            controls:{} as OrbitControls,
            stats:new Stats(),
        }
    },
    methods: {
        init() {

            this.container = (this.$refs.container as HTMLElement);
            //性能监视器
            this.container.appendChild(this.stats.dom);
            //渲染器
            this.renderer = new THREE.WebGLRenderer({ antialias: true });
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            this.renderer.outputEncoding = THREE.sRGBEncoding
            this.container.appendChild( this.renderer.domElement );

            const pmremGenerator = new THREE.PMREMGenerator(this.renderer);

            scene.background = new THREE.Color(0xbfe3dd);
            scene.environment = pmremGenerator.fromScene(new RoomEnvironment(), 0.04).texture;

            this.camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 100);
            this.camera.position.set(5, 2, 8);

            this.controls = new OrbitControls(this.camera, this.renderer.domElement);
            this.controls.target.set(0, 0.5, 0);
            this.controls.update();
            this.controls.enablePan = false;
            this.controls.enableDamping = true;

            const dracoLoader = new DRACOLoader();
            dracoLoader.setDecoderPath('/src/assets/draco/');
            const loader = new GLTFLoader();
            loader.setDRACOLoader(dracoLoader);
            loader.load('/src/assets/models/LittlestTokyo.glb', (gltf) => {
                console.log(gltf)
                const model = gltf.scene;
                model.position.set(1, 1, 0);
                model.scale.set(0.01, 0.01, 0.01);
                scene.add(toRaw(model));
                this.mixer = new THREE.AnimationMixer(model);
                this.mixer.clipAction(gltf.animations[0]).play();
                this.animate();
            }, undefined, function (e) {
                console.log(e)
            })

        },
        render() {
            this.renderer.render(scene, this.camera)
        },
        animate() {
            requestAnimationFrame(this.animate);
            const delta = this.clock.getDelta();
            this.mixer.update(delta);
            this.controls.update();
            this.stats.update();
            this.renderer.render(scene,this.camera)

        }
    },
    mounted() {
        this.init();
        const that = this
        window.onresize = function () {
            that.camera.aspect = window.innerWidth / window.innerHeight;
            that.camera.updateProjectionMatrix();
            that.renderer.setSize(window.innerWidth, window.innerHeight);
        }
    },
    components: {

    }
};
</script>


<style scoped></style>
