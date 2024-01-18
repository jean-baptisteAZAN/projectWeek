<script>
    import * as THREE from 'three';
    import {onMount} from 'svelte';
    import {GLTFLoader} from 'three/examples/jsm/loaders/GLTFLoader.js';
    import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls.js';

    let camera, scene, renderer;
    let loadedGltfScene = null;


    let colorIndex = 0;
    const colors = [new THREE.Color(0xff0000), new THREE.Color(0x0000ff), new THREE.Color(0x00ff00)];
    let currentColor = colors[0];
    let nextColor = colors[1];
    let lerpFactor = 0;

    function changeColor() {
        currentColor = colors[colorIndex % colors.length];
        nextColor = colors[(colorIndex + 1) % colors.length];
        colorIndex++;
        lerpFactor = 0;
    }

    setInterval(changeColor, 4000);

    onMount(async () => {
        scene = new THREE.Scene();
        scene.background = new THREE.Color('#F7FDFF');
        const aspectRatio = window.innerWidth / window.innerHeight;
        camera = new THREE.PerspectiveCamera(75, aspectRatio, 0.1, 1000);
        camera.position.set(5, 3, 0);

        renderer = new THREE.WebGLRenderer({antialias: true});
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;

        window.addEventListener('resize', onWindowResize, false);

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        document.body.appendChild(renderer.domElement);
        document.getElementById('three-container').appendChild(renderer.domElement);

        const loader = new GLTFLoader();
        loader.load(
            'mascotte.glb',
            function (gltf) {
                gltf.scene.traverse(function (object) {
                    if (object.isMesh) {
                        object.castShadow = true;
                        object.receiveShadow = true;
                    }
                });
                loadedGltfScene = gltf.scene;
                gltf.scene.position.set(0, (-1), 1);
                scene.add(gltf.scene);
            },
            undefined,
            function (error) {
                console.error(error);
            }
        );

        const light = new THREE.PointLight(0xffffff, 1);
        light.position.set(12, 3, 3);
        light.castShadow = true;
        scene.add(light);

        light.shadow.bias = -0.001;
        light.shadow.radius = 4;

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.minPolarAngle = Math.PI / 2;
        controls.maxPolarAngle = Math.PI / 2;
        controls.update();
        controls.enableZoom = false;


        function animate() {
            requestAnimationFrame(animate);
            if (loadedGltfScene) {
                lerpFactor += 0.01; // Ajustez cette valeur pour changer la vitesse de transition
                loadedGltfScene.traverse(function (object) {
                    if (object.isMesh && object.material) {
                        object.material.color.lerpColors(currentColor, nextColor, lerpFactor);
                    }
                });
            }

            controls.update();
            renderer.render(scene, camera);
        }

        animate();

    });
    function logCameraPosition() {
        if (camera) {
            console.log('Camera Position:', camera.position);
            // Pour plus de détails, tels que les valeurs individuelles de x, y, et z :
            console.log('X:', camera.position.x, 'Y:', camera.position.y, 'Z:', camera.position.z);
        } else {
            console.log('Camera is not defined');
        }
    }
</script>


<div id="three-container" class=" relative">
    <img src="/rewards.png" class="absolute z-50 w-20 top-40 left-5" alt="rewards" on:click={()=> logCameraPosition()}/>
    <img src="/personnalisation.png" class="absolute z-50 w-20 top-60 right-5" alt="perso"/>
    <img src="/games.png" alt="games" class="absolute z-50 w-20 top-96 left-5">
</div>

<style>
    :global(#three-container canvas) {
        position: absolute;
        top: 100px;
    }
</style>
