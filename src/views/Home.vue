<template>
  <div class="home">
    <canvas id="canvas" @mousemove="onMousemove"></canvas>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
import * as THREE from "three";
import { OBJLoader } from "three/examples/jsm/loaders/OBJLoader";

export default {
  name: "Home",
  components: {
    // HelloWorld,
  },
  data() {
    const w = window.innerWidth;
    const h = window.innerHeight;

    return {
      width: w,
      height: h,
      camera: new THREE.PerspectiveCamera(45, w / h, 0.1, 10000),
      light: new THREE.DirectionalLight(0xffffff, 1),
      point: new THREE.PointLight(0xffffff, 10, 1000, 1),
      loader: {
        obj: new OBJLoader(),
      },
      clock: new THREE.Clock(),
      mouse: { x: 0, y: 0 },
      canvas: null,
      renderer: null,
      rotate: { x: 0, y: 0 },
    };
  },
  created() {},
  mounted() {
    const scene = new THREE.Scene();
    this.scene = scene;

    this.canvas = document.querySelector("#canvas");
    this.renderer = new THREE.WebGLRenderer({
      canvas: this.canvas,
      // alpha: true,
      antialias: true,
    });

    this.renderer.setPixelRatio(window.devicePixelRatio > 1 ? 2 : 1);
    this.renderer.setSize(this.width, this.height);

    const params = {
      transmission: 0.95,
      roughness: 0,
      metalness: 0.5,
      thickness: 1,
    };

    // new THREE.TextureLoader().load("/bg.jpg", (tex) => {
    new THREE.TextureLoader().load("/tiger-crystal/bg.jpg", (tex) => {
      tex.magFilter = THREE.NearestFilter;
      tex.wrapT = THREE.RepeatWrapping;
      tex.wrapS = THREE.RepeatWrapping;
      tex.repeat.set(1, 3.5);

      tex.mapping = THREE.EquirectangularReflectionMapping;
      this.scene.background = tex;
      this.scene.environment = tex;
    });

    // this.loader.obj.load("/tiger.obj", (obj) => {
    this.loader.obj.load("/tiger-crystal/tiger.obj", (obj) => {
      console.log(obj);
      const material = new THREE.MeshPhysicalMaterial({
        transmission: params.transmission,
        roughness: params.roughness,
        metalness: params.metalness,
        thickness: params.thickness,
        transparent: true,
      });

      obj.traverse((child) => {
        if (child.isMesh) {
          child.material = material;
        }
      });

      obj.name = "tiger";
      this.scene.add(obj);
    });

    this.camera.position.set(0, 0, 1000);
    this.camera.zoom = 0.75;
    this.camera.updateProjectionMatrix();

    this.scene.add(this.light);
    this.scene.add(this.point);

    this.render();
  },
  methods: {
    render() {
      requestAnimationFrame(this.render);
      this.renderer.render(this.scene, this.camera);
      const tiger = this.scene.getObjectByName("tiger");
      if (tiger) {
        tiger.rotation.x = -this.mouse.y / 2;
        tiger.rotation.y = this.mouse.x / 2;
      }

      this.point.position.set(
        -this.mouse.x * this.width,
        -this.mouse.y * this.height,
        0
      );

      const targetRot = (this.mouse.x / this.width) * 360;
      const targetRotY = (this.mouse.y / this.width) * 360;
      // イージングの公式を用いて滑らかにする
      // 値 += (目標値 - 現在の値) * 減速値
      this.rotate.x += (targetRot - this.rotate.x) * 0.02;
      this.rotate.y += (targetRotY - this.rotate.y) * 0.02;

      // ラジアンに変換する
      const radian = {
        x: (this.rotate.x * Math.PI) / 180,
        y: (this.rotate.y * Math.PI) / 180,
      };

      this.camera.lookAt(new THREE.Vector3(0, 0, 0));
      this.camera.position.x = Math.sin(radian.x) * 50000;
      this.camera.position.y = Math.sin(radian.y) * 50000;
      // this.camera.position.z = 1000 * Math.cos(radian) + 750;
    },

    onMousemove(e) {
      this.mouse.x = (e.clientX / this.width) * 2 - 1;
      this.mouse.y = -(e.clientY / this.height) * 2 + 1;
    },
  },
};
</script>

<style>
#canvas {
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  top: 0;
}
</style>
