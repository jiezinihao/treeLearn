<script  lang="ts">
import * as THREE from "three"
//scene在three里设置configruate为false，
//而vue3监听用到的poxry必须要将configrate设置为true，所以此参数不能放在data里
//切scene.add方法内参数必须通过toRaw进行转化
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader"

import { toRaw } from "@vue/reactivity"
const scene = new THREE.Scene();
export default {
    data() {
        return {
            camera: {} as any,
            renderer: {} as any,
            geometry: {} as any,
            material: {} as any,
            cube: {} as any,
            controls: {} as any

        }
    },
    methods: {
        init() {
            this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            this.renderer = new THREE.WebGLRenderer();
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(this.renderer.domElement);

            let event = {
                onLoad(){
                    console.log("素材加载完成")
                },
                onProgress(url: any, num: any, total: any){
                    console.log("素材加载完成:" + url);
                    console.log("素材加载进度:" + num);
                    console.log("素材总数:" + total);
                },
                onErrror(e: any){
                    console.log("素材加载失败")
                    console.log(e)
                },
            };

            console.log(event)

            const loadingManager = new THREE.LoadingManager(event.onLoad, event.onProgress, event.onErrror);
            //导入纹理
            const textureLoader = new THREE.TextureLoader(loadingManager);
            const cubeTexture = textureLoader.load("/src/assets/test.jpg")
            // cubeTexture.offset.x=0.5;
            // cubeTexture.center.set(0.5,0.5)
            // cubeTexture.rotation = Math.PI / 4;
            // cubeTexture.repeat.set(1,1);
            // cubeTexture.wrapS = THREE.RepeatWrapping;
            // // cubeTexture.wrapT = THREE.MirroredRepeatWrapping;
            // cubeTexture.minFilter = THREE.NearestFilter;
            // cubeTexture.magFilter = THREE.NearestFilter
            const cubeTextureLoader = new THREE.CubeTextureLoader
            const envMapTexture = cubeTextureLoader.load([
                "/src/assets/file/sky.png",
                "/src/assets/file/sky.png",
                "/src/assets/file/sky.png",
                "/src/assets/file/sky.png",
                "/src/assets/file/sky.png",
                "/src/assets/file/sky.png",

            ]);
            const rgbeLoader = new RGBELoader();
            rgbeLoader.loadAsync("/src/assets/file/home.hdr").then((texture)=>{
                texture.mapping = THREE.EquirectangularReflectionMapping;
                scene.background = texture
                scene.environment = texture

            })
            console.log(envMapTexture)
            const geometry = new THREE.SphereGeometry(1, 20, 20);
            const material = new THREE.MeshStandardMaterial({ color: "#ffffff",metalness:0.7,roughness:0.1});
            const cube = new THREE.Mesh(geometry, material);
            scene.add(cube)
            const axesHelper = new THREE.AxesHelper(5);
            scene.add(axesHelper);
            //轨道控制器
            this.controls = new OrbitControls(this.camera, this.renderer.domElement);
            this.camera.position.set(4, 4, 4);
            //灯光
            const light = new THREE.AmbientLight(0x404040); // soft white light
            scene.add(light);

            //直线光源
            const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
            directionalLight.position.set(10, 10, 10);
            scene.add(directionalLight)
            // scene.background = envMapTexture;
            // scene.environment = envMapTexture;
            this.animate()
        },
        animate() {
            requestAnimationFrame(this.animate);
            this.renderer.render(scene, this.camera)
            this.controls.update();

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
