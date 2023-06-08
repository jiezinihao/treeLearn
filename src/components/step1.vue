<script  lang="ts">
import * as THREE from "three"
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
      geometry: {} as any,
      material: {} as any,
      cube: {} as any,
    }
  },
  methods: {
    init() {
      this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(this.renderer.domElement);
      this.geometry = new THREE.BoxGeometry(1, 1, 1);
      this.material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      this.cube = new THREE.Mesh(this.geometry, this.material);
      scene.add(toRaw(this.cube));
      this.camera.position.z = 5;
      this.animate();
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.cube.rotation.x += 0.01;
      this.cube.rotation.y += 0.01;
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
