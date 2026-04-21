<!DOCTYPE html>
<html>
<head>
  <title>3D Car Game</title>
  <style>
    body { margin: 0; overflow: hidden; }
  </style>
</head>
<body>

<script type="module">
import * as THREE from "https://cdn.jsdelivr.net/npm/three@0.152.2/build/three.module.js";

// Scene
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87CEEB);

// Camera
const camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);

// Renderer
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(innerWidth, innerHeight);
document.body.appendChild(renderer.domElement);

// Light
const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(10, 20, 10);
scene.add(light);

// Ground
const ground = new THREE.Mesh(
  new THREE.PlaneGeometry(200, 200),
  new THREE.MeshLambertMaterial({ color: 0x2e8b57 })
);
ground.rotation.x = -Math.PI / 2;
scene.add(ground);

// Car
const car = new THREE.Mesh(
  new THREE.BoxGeometry(2, 1, 4),
  new THREE.MeshLambertMaterial({ color: 0xff0000 })
);
car.position.y = 0.5;
scene.add(car);

// Controls
const keys = {};
addEventListener("keydown", e => keys[e.key.toLowerCase()] = true);
addEventListener("keyup", e => keys[e.key.toLowerCase()] = false);

let speed = 0;
let angle = 0;

function animate() {
  requestAnimationFrame(animate);

  // movement
  if (keys["w"]) speed += 0.01;
  if (keys["s"]) speed -= 0.01;
  speed *= 0.95;

  if (keys["a"]) angle += 0.03;
  if (keys["d"]) angle -= 0.03;

  car.rotation.y = angle;
  car.position.x += Math.sin(angle) * speed * 5;
  car.position.z += Math.cos(angle) * speed * 5;

  // camera follow
  camera.position.set(
    car.position.x - Math.sin(angle) * 8,
    5,
    car.position.z - Math.cos(angle) * 8
  );
  camera.lookAt(car.position);

  renderer.render(scene, camera);
}

animate();

// resize
addEventListener("resize", () => {
  camera.aspect = innerWidth/innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
</script>

</body>
</html>
