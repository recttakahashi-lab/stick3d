<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no">
<title>Stickman 3D Flash</title>
<style>
body{margin:0;overflow:hidden;background:#000}
</style>
</head>
<body>

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>
<script>
let scene,camera,renderer;
let player,boss;
let state="run";
let speed=0.15, slow=1;

init();
animate();

function init(){
  scene=new THREE.Scene();
  scene.fog=new THREE.Fog(0x000000,5,25);

  camera=new THREE.PerspectiveCamera(60,innerWidth/innerHeight,0.1,100);
  camera.position.set(0,2,5);

  renderer=new THREE.WebGLRenderer({antialias:true});
  renderer.setSize(innerWidth,innerHeight);
  document.body.appendChild(renderer.domElement);

  const light=new THREE.DirectionalLight(0xffffff,1.2);
  light.position.set(5,10,5);
  scene.add(light);
  scene.add(new THREE.AmbientLight(0xffffff,0.4));

  const floor=new THREE.Mesh(
    new THREE.PlaneGeometry(20,100),
    new THREE.MeshStandardMaterial({color:0x111111})
  );
  floor.rotation.x=-Math.PI/2;
  floor.position.z=-40;
  scene.add(floor);

  player=createStick();
  player.position.y=1;
  scene.add(player);

  boss=createStick(1.5);
  boss.position.set(0,1,-30);
  scene.add(boss);

  addEventListener("resize",()=>{
    camera.aspect=innerWidth/innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(innerWidth,innerHeight);
  });

  addEventListener("touchstart",()=>{
    if(state==="end") location.reload();
  });
}

function createStick(scale=1){
  const g=new THREE.Group();
  const mat=new THREE.MeshStandardMaterial({color:0xffffff});

  const body=new THREE.Mesh(
    new THREE.CylinderGeometry(0.1*scale,0.1*scale,1.5*scale),mat);
  body.position.y=0.75*scale;
  g.add(body);

  const head=new THREE.Mesh(
    new THREE.SphereGeometry(0.25*scale),mat);
  head.position.y=1.7*scale;
  g.add(head);

  const armGeo=new THREE.CylinderGeometry(0.05*scale,0.05*scale,1*scale);
  const armL=new THREE.Mesh(armGeo,mat);
  armL.rotation.z=Math.PI/2;
  armL.position.set(-0.6*scale,1.2*scale,0);
  g.add(armL);

  const armR=armL.clone();
  armR.position.x=0.6*scale;
  g.add(armR);

  return g;
}

let timer=0;
function animate(){
  requestAnimationFrame(animate);
  timer+=0.02*slow;

  if(state==="run"){
    player.position.z-=speed;
    camera.position.z-=speed;
    if(player.position.z<-25){
      state="boss";
      slow=0.3;
    }
  }

  if(state==="boss"){
    camera.position.x=Math.sin(timer)*1.5;
    camera.lookAt(boss.position);
    boss.rotation.y+=0.1;
    if(timer>3){
      state="end";
      slow=0.1;
    }
  }

  if(state==="end"){
    camera.position.z-=0.05;
  }

  renderer.render(scene,camera);
}
</script>
</body>
</html>
