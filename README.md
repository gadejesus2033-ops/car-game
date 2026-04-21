<!DOCTYPE html>
<html>
<head>
  <title>3D Car Game</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; }
  </style>
</head>
<body>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>

<script>
// Scene
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87CEEB);

// Camera
const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);

// Renderer
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Light
const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(5,10,5);
scene.add(light);

// Ground
const groundGeo = new THREE.PlaneGeometry(200, 200);
const groundMat = new THREE.MeshLambertMaterial({ color: 0x228B22 });
const ground = new THREE.Mesh(groundGeo, groundMat);
ground.rotation.x = -Math.PI / 2;
scene.add(ground);

// Car
const car = new THREE.Group();

// Body
const bodyGeo = new THREE.BoxGeometry(2, 0.5, 4);
const bodyMat = new THREE.MeshLambertMaterial({ color: 0xff0000 });
const body = new THREE.Mesh(bodyGeo, bodyMat);
body.position.y = 0.5;
car.add(body);

// Wheels
function createWheel(x, z) {
  const geo = new THREE.CylinderGeometry(0.4, 0.4, 0.5, 16);
  const mat = new THREE.MeshLambertMaterial({ color: 0x333333 });
  const wheel = new THREE.Mesh(geo, mat);
  wheel.rotation.z = Math.PI / 2;
  wheel.position.set(x, 0.4, z);
  return wheel;
}

car.add(createWheel(-1, 1.5));
car.add(createWheel(1, 1.5));
car.add(createWheel(-1, -1.5));
car.add(createWheel(1, -1.5));

scene.add(car);

// Controls
const keys = {};

document.addEventListener("keydown", (e) => keys[e.key.toLowerCase()] = true);
document.addEventListener("keyup", (e) => keys[e.key.toLowerCase()] = false);

let speed = 0;
let angle = 0;

// Game loop
function animate() {
  requestAnimationFrame(animate);

  // Forward / backward
  if (keys["w"]) speed += 0.002;
  if (keys["s"]) speed -= 0.002;

  // Friction
  speed *= 0.98;

  // Turning
  if (keys["a"]) angle += 0.03;
  if (keys["d"]) angle -= 0.03;

  // Move car
  car.rotation.y = angle;
  car.position.x += Math.sin(angle) * speed * 100;
  car.position.z += Math.cos(angle) * speed * 100;

  // Camera follow
  camera.position.x = car.position.x - Math.sin(angle) * 8;
  camera.position.z = car.position.z - Math.cos(angle) * 8;
  camera.position.y = 5;
  camera.lookAt(car.position);

  renderer.render(scene, camera);
}

animate();

// Resize fix
window.addEventListener("resize", () => {
  camera.aspect = window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
</script>

</body>
</html>
