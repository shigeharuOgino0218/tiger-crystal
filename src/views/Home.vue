<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <canvas id="canvas"></canvas>
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
      scene: new THREE.Scene(),
      camera: new THREE.PerspectiveCamera(45, w / h, 0.1, 1000),
      light: new THREE.DirectionalLight(0xffffff, 1),
      loader: {
        obj: new OBJLoader()
      },
      clock: new THREE.Clock(),
      canvas: null,
      renderer: null
    };
  },
  created() {},
  mounted() {
    this.canvas = document.querySelector("#canvas");
    this.renderer = new THREE.WebGLRenderer({
      canvas: this.canvas,
      // alpha: true,
      antialias: true,
    });

    this.renderer.setPixelRatio(window.devicePixelRatio > 1 ? 2 : 1);
    this.renderer.setSize(this.width, this.height);

    this.loader.obj.load("/tiger.obj", (obj) => {
      console.log(obj);
      this.scene.add(obj)
    })
  },
};
</script>
