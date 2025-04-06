# WebXR/AR/VR Development

This section covers development of WebXR, Augmented Reality (AR), and Virtual Reality (VR) applications.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](#webxr-development) (this file)
- [CI/CD Integration](./USAGE-CICD.md)

## WebXR with Three.js

Three.js is a JavaScript library for creating 3D graphics in the browser.

```bash
# Create a WebXR project with Three.js
mkdir webxr-three && cd webxr-three

# Create index.html
cat > index.html << EOF
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>WebXR with Three.js</title>
  <style>
    body { margin: 0; }
    canvas { display: block; }
  </style>
</head>
<body>
  <script type="module">
    import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';
    import { VRButton } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/webxr/VRButton.js';
    
    // Setup scene
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x505050);
    
    // Setup camera
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(0, 1.6, 3);
    
    // Setup renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.xr.enabled = true;
    document.body.appendChild(renderer.domElement);
    document.body.appendChild(VRButton.createButton(renderer));
    
    // Add a cube
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    cube.position.set(0, 1.5, -2);
    scene.add(cube);
    
    // Add lights
    const light = new THREE.HemisphereLight(0xffffff, 0xbbbbff, 1);
    scene.add(light);
    
    // Animation loop
    renderer.setAnimationLoop(function () {
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    });
    
    // Handle window resize
    window.addEventListener('resize', function () {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });
  </script>
</body>
</html>
EOF

# Serve the project
python3 -m http.server 3000
```

## WebXR with A-Frame

A-Frame is a web framework for building virtual reality experiences.

```bash
# Create a basic A-Frame project
mkdir aframe-project && cd aframe-project

# Create index.html
cat > index.html << EOF
<!DOCTYPE html>
<html>
  <head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
  </head>
  <body>
    <a-scene>
      <a-box position="-1 0.5 -3" rotation="0 45 0" color="#4CC3D9"></a-box>
      <a-sphere position="0 1.25 -5" radius="1.25" color="#EF2D5E"></a-sphere>
      <a-cylinder position="1 0.75 -3" radius="0.5" height="1.5" color="#FFC65D"></a-cylinder>
      <a-plane position="0 0 -4" rotation="-90 0 0" width="4" height="4" color="#7BC8A4"></a-plane>
      <a-sky color="#ECECEC"></a-sky>
    </a-scene>
  </body>
</html>
EOF

# Serve the project
python3 -m http.server 3000
```

## Augmented Reality with AR.js

AR.js is a lightweight library for Augmented Reality on the Web.

```bash
# Create an AR.js project
mkdir arjs-project && cd arjs-project

# Create index.html
cat > index.html << EOF
<!DOCTYPE html>
<html>
  <head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js"></script>
  </head>
  <body style="margin: 0; overflow: hidden;">
    <a-scene embedded arjs="sourceType: webcam; debugUIEnabled: false;">
      <a-marker preset="hiro">
        <a-box position="0 0.5 0" material="color: red;"></a-box>
      </a-marker>
      <a-entity camera></a-entity>
    </a-scene>
  </body>
</html>
EOF

# Serve the project
python3 -m http.server 3000
```

## React Three Fiber

React Three Fiber is a React renderer for Three.js.

```bash
# Create a new React app
npx create-react-app react-three-fiber-app
cd react-three-fiber-app

# Install Three.js libraries
npm install three @react-three/fiber @react-three/drei

# Replace src/App.js
cat > src/App.js << EOF
import React, { useRef } from 'react';
import { Canvas, useFrame } from '@react-three/fiber';
import { OrbitControls } from '@react-three/drei';
import './App.css';

function Box(props) {
  const meshRef = useRef();
  
  useFrame((state, delta) => {
    meshRef.current.rotation.x += delta;
    meshRef.current.rotation.y += delta * 0.5;
  });
  
  return (
    <mesh
      {...props}
      ref={meshRef}
      scale={1.5}
    >
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial color={props.color || 'orange'} />
    </mesh>
  );
}

function App() {
  return (
    <div className="App" style={{ width: '100vw', height: '100vh' }}>
      <Canvas>
        <ambientLight intensity={0.5} />
        <spotLight position={[10, 10, 10]} angle={0.15} penumbra={1} />
        <Box position={[-1.2, 0, 0]} color="hotpink" />
        <Box position={[1.2, 0, 0]} color="lightblue" />
        <OrbitControls />
      </Canvas>
    </div>
  );
}

export default App;
EOF

# Start the development server
npm start
```

## WebXR with React Three Fiber and VR Support

```bash
# Create a new React app
npx create-react-app react-three-vr-app
cd react-three-vr-app

# Install Three.js libraries
npm install three @react-three/fiber @react-three/drei @react-three/xr

# Replace src/App.js
cat > src/App.js << EOF
import React, { useState } from 'react';
import { Canvas } from '@react-three/fiber';
import { OrbitControls } from '@react-three/drei';
import { VRButton, XR, Controllers, Hands } from '@react-three/xr';
import './App.css';

function Box(props) {
  const [hovered, setHovered] = useState(false);
  const [clicked, setClicked] = useState(false);
  
  return (
    <mesh
      {...props}
      onClick={() => setClicked(!clicked)}
      onPointerOver={() => setHovered(true)}
      onPointerOut={() => setHovered(false)}
      scale={clicked ? 1.5 : 1}
    >
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial color={hovered ? 'hotpink' : props.color || 'orange'} />
    </mesh>
  );
}

function App() {
  return (
    <>
      <VRButton />
      <Canvas style={{ height: '100vh' }}>
        <XR>
          <ambientLight intensity={0.5} />
          <spotLight position={[10, 10, 10]} angle={0.15} penumbra={1} />
          
          <Box position={[-1.2, 1, -2]} color="blue" />
          <Box position={[1.2, 1, -2]} color="red" />
          
          <Controllers />
          <Hands />
          
          <mesh rotation={[-Math.PI / 2, 0, 0]} position={[0, 0, 0]}>
            <planeGeometry args={[10, 10]} />
            <meshStandardMaterial color="#7BC8A4" />
          </mesh>
        </XR>
        <OrbitControls />
      </Canvas>
    </>
  );
}

export default App;
EOF

# Start the development server
npm start
