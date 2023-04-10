<template>
    <div>
        <div ref="step3_cube">
        </div>
    </div>
</template>

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
            rollOverMesh: {} as any,
            plane: {} as any,
            cubeGeo: {} as THREE.BoxGeometry,
            cubeMaterial: {} as THREE.MeshBasicMaterial,
            raycaster: {} as THREE.Raycaster,
            pointer: {} as THREE.Vector2,
            objects: [] as any,
            step3_cube: {} as HTMLElement,
            vector3: {} as THREE.Vector3,
            isShiftDown: false as boolean
        }
    },
    methods: {
        init() {
            this.camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 10000);

            this.camera.position.set(400, 800, 1200);
            this.camera.lookAt(0, 0, 0);

            scene.background = new THREE.Color(0xf0f0f0)

            const gridHelper = new THREE.GridHelper(1000, 20);
            scene.add(gridHelper);


            //提示箱子
            const rollOverGeo = new THREE.BoxGeometry(50, 50, 50);
            const rollOverMeterial = new THREE.MeshBasicMaterial({ color: 0xff0000, opacity: 0.5, transparent: true });
            this.rollOverMesh = new THREE.Mesh(rollOverGeo, rollOverMeterial);
            scene.add(toRaw(this.rollOverMesh));

            //蒙层
            const geometry = new THREE.PlaneGeometry(1000, 1000);
            geometry.rotateX(- Math.PI / 2);
            this.plane = new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({ color: 0x00ff00, visible: false }));
            scene.add(this.plane);

            this.objects.push(this.plane);
            //cube
            this.cubeGeo = new THREE.BoxGeometry(50, 50, 50);
            this.cubeMaterial = new THREE.MeshBasicMaterial({ color: 0xfeb74c, map: new THREE.TextureLoader().load('/src/assets/square-outline-textured.png') })

            this.raycaster = new THREE.Raycaster();
            this.pointer = new THREE.Vector2();

            //linght
            const amLight = new THREE.AmbientLight(0x606060);
            scene.add(amLight);

            const directionalLight = new THREE.DirectionalLight(0xffffff);
            directionalLight.position.set(0, 0.75, 0.5).normalize()
            scene.add(directionalLight);

            this.renderer = new THREE.WebGLRenderer({ antialias: true });
            this.renderer.setPixelRatio(window.devicePixelRatio);
            this.renderer.setSize(window.innerWidth, window.innerHeight);

            this.step3_cube = (this.$refs.step3_cube as HTMLElement);
            this.step3_cube.appendChild(this.renderer.domElement);

            this.step3_cube.addEventListener('pointermove', this.onPointerMove);
            this.step3_cube.addEventListener('pointerdown', this.onPointerDown);
            document.addEventListener("keydown", this.onKeyDown);
            document.addEventListener("keyup", this.onKeyUp);
            window.addEventListener("resize", this.onResize);

        },

        onPointerMove(event: any) {
            this.pointer.set((event.clientX / window.innerWidth) * 2 - 1, - (event.clientY / window.innerHeight) * 2 + 1);
            this.raycaster.setFromCamera(this.pointer, this.camera);
            const intersects = this.raycaster.intersectObjects(this.objects, false);
            if (intersects.length > 0) {
                this.rollOverMesh.position.copy(intersects[0].point).add(intersects[0].face?.normal);
                this.rollOverMesh.position.divideScalar(50).floor().multiplyScalar(50).addScalar(25);
                this.render()
            }
        },
        onPointerDown(event: any) {
            this.pointer.set((event.clientX / window.innerWidth) * 2 - 1, - (event.clientY / window.innerHeight) * 2 + 1);
            this.raycaster.setFromCamera(this.pointer, this.camera);
            const intersects = this.raycaster.intersectObjects(this.objects, false);
            const intersect = intersects[0]
            if (intersects.length > 0) {
                if (this.isShiftDown) {
                    // console.log(intersects)
                    if (intersects[0].object !== this.plane) {
                        scene.remove(intersect.object);
                        this.deleteGroup(intersect.object)
                        console.log(intersect.object)
                        this.objects.splice(this.objects.indexOf(intersect.object), 1);
                    }
                } else {
                    if (intersects.length > 0) {
                        const cubeMesh = new THREE.Mesh(this.cubeGeo, this.cubeMaterial);
                        cubeMesh.position.copy(intersects[0].point).add(intersects[0].face?.normal || this.vector3).divideScalar(50).floor().multiplyScalar(50).addScalar(25);
                        scene.add(toRaw(cubeMesh));
                        this.objects.push(cubeMesh); // 将物体对象添加进object
                    }
                }
                this.render();
                console.log(scene)
            }

        },
        // 删除group，释放内存
        deleteGroup(group:any) {
            if (!group) return
            // 删除掉所有的模型组内的mesh
            group.traverse(function (item:any) {
                if (item instanceof THREE.Mesh) {
                    if (Array.isArray(item.material)) {
                        item.material.forEach(a => {
                            a.dispose()
                        })
                    } else {
                        item.material.dispose() // 删除材质
                    }
                    item.geometry.dispose() // 删除几何体
                }
            })
        },
        onKeyDown(event: any) {
            switch (event.keyCode) {
                case 16:
                    this.isShiftDown = true
            }
            console.log(this.isShiftDown)
        },
        onKeyUp(event: any) {
            switch (event.keyCode) {
                case 16:
                    this.isShiftDown = false
            }
            console.log(this.isShiftDown)

        },
        onResize() {
            this.camera.aspect = window.innerWidth / window.innerHeight;
            this.camera.updateProjectionMatrix();
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            this.render()
        },
        render() {
            this.renderer.render(scene, this.camera)
        }
    },
    created() {
    },
    mounted() {
        this.init();
        this.render()
    },
    components: {

    }
};
</script>

<style scoped></style>
