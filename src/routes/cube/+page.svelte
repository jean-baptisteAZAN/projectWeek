<script>
    import * as THREE from 'three';
    import * as SC from 'svelte-cubed';

    let materials = [];
    let textures = [];
    let raycaster = new THREE.Raycaster();
    let mouse = new THREE.Vector2();
    let showModal = false

    // Cette fonction initialise les textures et les matériaux
    function initTexturesAndMaterials() {
        const imagePaths = [
            '/mer.jpeg',
            '/desert.jpeg',
            '/tour-eiffel.jpg',
            '/gif.gif',
            '/mont_blanc.jpeg',
            '/canyon.avif',
        ];

        const loader = new THREE.TextureLoader();
        imagePaths.forEach((path, index) => {
            loader.load(path, (loadedTexture) => {
                textures[index] = loadedTexture;
                materials = textures.map(texture => new THREE.MeshBasicMaterial({ map: texture }));
            });
        });
    }

    // Assurez-vous d'être côté client avant d'exécuter initTexturesAndMaterials
    if (typeof window !== 'undefined') {
        initTexturesAndMaterials();
    }

    // Fonction pour gérer le clic sur le canvas
    function onMouseClick(event) {
        // Calculer la position de la souris en coordonnées normalisées (-1 à +1) pour le raycaster
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

        raycaster.setFromCamera(mouse, camera); // Assurez-vous que "camera" est la caméra que vous utilisez

        // Trouver les objets qui intersectent le rayon
        let intersects = raycaster.intersectObjects(scene.children); // Assurez-vous que "scene" est votre scène

        if (intersects.length > 0) {
            // Le premier objet intersecté est le plus proche
            let object = intersects[0].object;

            // Si l'objet cliqué est notre bouton (vous pouvez définir un nom pour le bouton lors de sa création pour faciliter cette vérification)
            if (object.name === "button") {
                // Afficher l'élément HTML en absolu ici
                showModal = true;
            }
        }
    }
</script>

<!-- Ajouter la référence à l'élément canvas pour l'écouteur d'événements -->
<SC.Canvas antialias background={new THREE.Color('papayawhip')}>
    <SC.Mesh geometry={new THREE.BoxGeometry(2, 2, 2)} material={materials} />
    <SC.PerspectiveCamera position={[1, 1, 5]} />
    <SC.OrbitControls enableZoom={false} />
</SC.Canvas>

<!-- Élément HTML à afficher en absolu -->
{#if showModal}
    <div class="absolute top-0 left-0 w-full h-full bg-white bg-opacity-80 flex items-center justify-center">
        Votre contenu ici
    </div>
{/if}
