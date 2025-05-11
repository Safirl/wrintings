<script lang="ts">
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import GUI from 'lil-gui';
	import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls.js'

	let container: HTMLDivElement;

	onMount(() => {
		/**
		 * DEBUG Object
		 */
		const gui = new GUI();
		const debugObject = {
			color: '#000',
			cameraFov: 75,
			farClip: 1000
		};

		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(
			debugObject.cameraFov,
			container.clientWidth / container.clientHeight,
			0.1,
			debugObject.farClip
		);
		const controls = new OrbitControls(camera, document.getElementById("container"))
		camera.position.z = 5;
		const renderer = new THREE.WebGLRenderer();
		renderer.setSize(container.clientWidth, container.clientHeight);
		container.appendChild(renderer.domElement);

		const geometry = new THREE.SphereGeometry(3, 32, 32);
		// const material = new THREE.MeshDepthMaterial()
		const material = new THREE.MeshBasicMaterial(/*{ color: 0xffffff, wireframe: false }*/);
		const planeMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff, wireframe: false });
		// planeMaterial.side = THREE.DoubleSide
		const sphere = new THREE.Mesh(geometry, material);

		const planeGeometry = new THREE.PlaneGeometry(2, 1)
		const plane = new THREE.Mesh(planeGeometry, planeMaterial);
		plane.position.z += 3;

		scene.add(sphere, plane);

		scene.fog = new THREE.Fog("#3a6ea6", 0.1, 0.905);

		// Sizes
		const sizes = {
			width: window.innerWidth,
			height: window.innerHeight
		};

		window.addEventListener('resize', () => {
			// Update sizes
			sizes.width = window.innerWidth;
			sizes.height = window.innerHeight;

			// Update camera
			camera.aspect = sizes.width / sizes.height;
			camera.updateProjectionMatrix();

			// Update renderer
			renderer.setSize(sizes.width, sizes.height);
		});
	

		function animate() {
			requestAnimationFrame(animate);
			// sphere.rotation.y += 0.01;è
			renderer.render(scene, camera);
		}

		animate();

		/**
		 * DEBUG FUNCTIONS
		*/
		// const gui = new GUI();
		// const debugObject = {
		// 	color: '#3a6ea6',
		// 	cameraFov: 75,
		// 	farClip: 1.519
		// };
		gui.add(camera.position, 'z', 0, 10, 0.01).name('cameraDistance');
		// gui.addColor(debugObject, 'color').onChange((newColor: string) => {
		// 	console.log(material.color)
		// 	material.color.set(newColor);
		// 	renderer.render(scene, camera);
		// });
		gui.add(debugObject, 'cameraFov', 0, 150, 0.01).onChange((newFov: number) => {
			camera.fov = newFov;
			camera.updateProjectionMatrix();
		});
		gui.add(debugObject, 'farClip', 0, 10, 0.001).onChange((newFarClip: number) => {
			camera.far = newFarClip;
			camera.updateProjectionMatrix();
		});
	});
</script>

<div id="container" bind:this={container}></div>

<style lang="scss">
	div {
		min-width: 100vw;
		min-height: 100vh;
	}
</style>
