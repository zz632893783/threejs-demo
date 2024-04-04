<template>
    <div class="page">
        <div class="webgl-container" ref="containerRef"></div>
    </div>
</template>
<script setup>
import Stats from 'stats-js';
import * as THREE from 'three';
import { ref, onMounted, onUpdated } from 'vue';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { CSS3DRenderer, CSS3DObject } from 'three/addons/renderers/CSS3DRenderer.js';
// webgl 容器 dom 对象
const containerRef = ref();
// webgl 渲染器
let webglRender = null;
// html 渲染器
let htmlRender = null;
// 场景
let scene = null;
// 相机
let camera = null;
// 滑轨控制器
// controls: null,
// 光源
let light = null;
// 状态监听器
let stats = null;
// 创建 webgl 渲染器
const createWebglRender = () => {
    const canvas = document.createElement('canvas');
    canvas.width = containerRef.value.offsetWidth;
    canvas.height = containerRef.value.offsetHeight;
    containerRef.value.appendChild(canvas);
    webglRender = new THREE.WebGLRenderer({ canvas });
    // webglRender.setClearColor(new THREE.Color(0xEEEEEE), 1.0);
    webglRender.setSize(containerRef.value.offsetWidth, containerRef.value.offsetHeight);
    webglRender.shadowMap.enabled = true;
};
// 创建 html 渲染器
const createHtmlRender = () => {
    htmlRender = new CSS3DRenderer();
    htmlRender.setSize(containerRef.value.offsetWidth, containerRef.value.offsetHeight);
    htmlRender.domElement.style.position = 'absolute';
    htmlRender.domElement.style.top = htmlRender.domElement.style.left = 0;
    containerRef.value.appendChild(htmlRender.domElement);
};
// 创建场景
const createScene = () => (scene = new THREE.Scene());
// 创建相机
const createCamera = () => {
    camera = new THREE.PerspectiveCamera(45, containerRef.value.offsetWidth / containerRef.value.offsetHeight, 0.1, 2000);
    camera.position.set(300, 400, 300);
    camera.lookAt(scene.position);
    // this.controls = new OrbitControls(this.camera, this.$refs.canvas)
    scene.add(camera);
};
// 创建性能监听器
const createStats = () => {
    stats = new Stats();
    stats.setMode(0);
    stats.domElement.style.position = 'fixed';
    stats.domElement.style.top = 0;
    stats.domElement.style.right = 0;
    stats.domElement.style.left = 'auto';
    document.body.appendChild(stats.domElement)
};
// 创建光源
const createLight = () => {
    light = new THREE.DirectionalLight(0xffffff);
    light.position.set(-40, 60, -10);
    light.castShadow = true;
    scene.add(light);
};
// 创建模型
const createModel = () => {
    const axis = new THREE.AxesHelper(200);
    scene.add(axis);
    // 创建加入的 dom 元素
    const appendElement = document.createElement('div');
    appendElement.style.color = 'red';
    appendElement.style.fontSize = '32px';
    appendElement.innerHTML = 'hello world';
    const htmlMesh = new CSS3DObject(appendElement);
    htmlMesh.position.z = 100;
    scene.add(htmlMesh);
};
// 动画 id
let animation;
// 周期渲染
const setAnimation = () => {
    animation = window.requestAnimationFrame(setAnimation);
    webglRender.render(scene, camera);
    htmlRender.render(scene, camera);
    stats.update();
};
// 创建滑轨
const createControls = () => {
    new OrbitControls(camera, htmlRender.domElement);
};
onMounted(() => {
    createWebglRender();
    createHtmlRender();
    createScene();
    createCamera();
    createLight();
    createStats();
    createModel();
    createControls();
    setAnimation();
});
onUpdated(() => {
    animation && window.cancelAnimationFrame(animation);
    setAnimation();
});
</script>
<style lang="scss" scoped>
.page {
    width: 100vw;
    height: 100vh;
    position: relative;
    .webgl-container {
        width: 100%;
        height: 100%;
        position: relative;
    }
}
</style>
