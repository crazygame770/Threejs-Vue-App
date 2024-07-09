<template>
  <div></div>
</template>

<script setup>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import * as THREE from "three";

let lon = 0,
				lat = 0,
				phi = 0, theta = 0;

// const shaderMaterial = new THREE.ShaderMaterial({
//   vertexShader: `
//     void main()
//     {
//         vec4 mvPosition = modelViewMatrix * vec4(position, 1.0 );
//         gl_Position = projectionMatrix * mvPosition;
//     }`, 
//   fragmentShader: `
//     uniform vec2 iResolution;

//     void main() {
//       vec2 uv = gl_FragCoord.xy / iResolution.xy;

//       vec3 color1 = vec3(0.0, 0.0, 0.0);
//       vec3 color2 = vec3(1.0, 1.0, 1.0);

//       vec3 pixel = color2;
      
//       float t = 1.0;
      
//       if(uv.x > t - 1.0 && uv.x < t - 0.9){
//           pixel = color2;
//       } 
//       else if(uv.x > t - 0.8 && uv.x < t - 0.7){
//           pixel = color2;
//       } 
//       else if(uv.x > t - 0.6 && uv.x < t - 0.5){
//           pixel = color2;
//       } 
//       else if(uv.x > t - 0.4 && uv.x < t - 0.3){
//           pixel = color2;
//       } 
//       else if(uv.x > t - 0.2 && uv.x < t - 0.1){
//           pixel = color2;
//       }  
     
//       gl_FragColor = vec4(pixel, 1.0);
//   }`
// })

const scene = new THREE.Scene();

const width = window.innerWidth;
const height = window.innerHeight;
const camera = new THREE.PerspectiveCamera(95, width / height, 0.01, 1000);

const cubeGeometry = new THREE.BoxGeometry(50, 50, 50);
const cubeMaterial = new THREE.MeshBasicMaterial({ color: "blue" });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);

const circleGeometry = new THREE.SphereGeometry( 500, 60, 60 );

// invert the geometry on the x-axis so that all of the faces point inward
circleGeometry.scale( - 1, 1, 1 );
circleGeometry.rotateX(Math.PI)

const texture = new THREE.TextureLoader().load('background.jpg');
console.log(texture);

texture.colorSpace = THREE.SRGBColorSpace;
const circleMaterial = new THREE.MeshBasicMaterial({ map: texture });

const circleMesh = new THREE.Mesh( circleGeometry, circleMaterial );

console.log(circleMesh)
camera.position.set(0, 0, 10);

scene.add(camera);
scene.add(cube);
scene.add( circleMesh );

console.log(scene);

const renderer = new THREE.WebGLRenderer();
renderer.setSize(width, height);
document.body.appendChild(renderer.domElement);

const controls = new OrbitControls(camera, renderer.domElement);
// const gui = new dat.GUI();
controls.update();

// gui.add(cube.position, "x").min(0).max(10).step(0.01);

window.addEventListener("resize", () => {
  const width = window.innerWidth;
  const height = window.innerHeight;
  const pixelRatio = window.devicePixelRatio;

  camera.aspect = width / height;
  camera.updateProjectionMatrix();

  renderer.setSize(width, height);
  renderer.setPixelRatio(pixelRatio);
});

function animate() {
  requestAnimationFrame(animate);

  lon += 0.1;

  lat = Math.max( - 85, Math.min( 85, lat ) );
  phi = THREE.MathUtils.degToRad( 90 - lat );
  theta = THREE.MathUtils.degToRad( lon );

  const x = 500 * Math.sin( phi ) * Math.cos( theta );
  const y = 500 * Math.cos( phi );
  const z = 500 * Math.sin( phi ) * Math.sin( theta );
  
  cube.position.x = 300 * Math.sin( phi ) * Math.cos( theta );
  cube.position.z = 300 * Math.sin( phi ) * Math.sin( theta );
  cube.rotation.y = lon/100;
  camera.lookAt( x, y, z );
  renderer.render(scene, camera);
}

animate();
</script>

<style>
</style>
