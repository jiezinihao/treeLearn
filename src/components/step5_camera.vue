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
           
        }
    },
    methods: {
        init() {

           

        },
        animate() {

        }
    },
    mounted() {
        this.init();
        const that = this
        window.onresize = function () {
        }
    },
    components: {

    }
};
</script>


<style scoped></style>
