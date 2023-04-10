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
        }
    },
    methods: {
        init() {
            this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            this.renderer = new THREE.WebGLRenderer();
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(this.renderer.domElement);
        },
        render() {
            this.renderer.render(scene, this.camera)
        }
    },
    created() {
        this.init();
    },
    components: {

    }
};
</script>
<template></template>

<style scoped></style>
