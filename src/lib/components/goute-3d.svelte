<script>
    import * as THREE from 'three';
    import {onMount} from 'svelte';
    import {GLTFLoader} from 'three/examples/jsm/loaders/GLTFLoader.js';
    import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls.js';

    let camera, scene, renderer;

    onMount(async () => {
        scene = new THREE.Scene();
        scene.background = new THREE.Color('#F7FDFF');
        const aspectRatio = window.innerWidth / window.innerHeight;
        camera = new THREE.PerspectiveCamera(75, aspectRatio, 0.1, 1000);
        camera.position.set(0, (-400), (100));

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
            'goutte_eau.gltf',
            function (gltf) {
                gltf.scene.traverse(function (object) {
                    if (object.isMesh) {
                        object.castShadow = true;
                        object.receiveShadow = true;
                    }
                });
                scene.add(gltf.scene);
            },
            undefined,
            function (error) {
                console.error(error);
            }
        );

        const light = new THREE.PointLight(0xffffff, 1);
        light.position.set(3, 3, 3);
        light.castShadow = true;
        scene.add(light);

        light.shadow.bias = -0.002;
        light.shadow.radius = 4;

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.update();

        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            renderer.render(scene, camera);
        }

        animate();
    });
</script>


<div id="three-container" class=" relative">
    <img src="/rewards.png" class="absolute z-50 w-20 top-40 left-5" alt="rewards"/>
    <img src="/personnalisation.png" class="absolute z-50 w-20 top-60 right-5" alt="perso"/>
    <img src="/games.png" alt="games" class="absolute z-50 w-20 top-96 left-5">
</div>

<style>
    :global(#three-container canvas) {
        position: absolute;
        top: 100px;
    }
</style>
