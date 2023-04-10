<script  lang="ts">
import * as THREE from "three"
//scene在three里设置configruate为false，
//而vue3监听用到的poxry必须要将configrate设置为true，所以此参数不能放在data里
//切scene.add方法内参数必须通过toRaw进行转化
import {toRaw} from "@vue/reactivity"
const scene = new THREE.Scene();
export default {
  data() {
    return {
      camera:{} as any,
      renderer:{} as any,
      geometry:{} as any,
      material:{} as any,
      cube:{} as any
    }
  },
  methods: {
    init() {
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth,window.innerHeight);
        document.body.appendChild(renderer.domElement);

        const camera = new THREE.PerspectiveCamera(45,window.innerWidth/window.innerHeight,1,500);
        camera.position.set(0,0,100);
        camera.lookAt(0,0,0);

        const meterial = new THREE.LineBasicMaterial({color:0xfeb74c});
        const points = [];
        points.push(new THREE.Vector3(-10,0,0));
        points.push( new THREE.Vector3( 0, 10, 0 ) );
        points.push( new THREE.Vector3( 10, 0, 0 ) );

        const geometry =  new THREE.BufferGeometry().setFromPoints(points);
        const line = new THREE.Line(geometry,meterial);
        scene.add(toRaw(line));
        renderer.render(scene,camera);
    },
  },
  created() {
    this.init();
  },
  components: {

  }
};
</script>
<template>
 
</template>

<style scoped>
</style>
