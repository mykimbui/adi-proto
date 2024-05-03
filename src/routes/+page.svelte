<script>
	import { onMount } from 'svelte';
	import image1 from '$lib/images/section1/img1.jpg';
	import image2 from '$lib/images/section1/img2.jpg';
	import image3 from '$lib/images/section1/img3.jpg';
	import image4 from '$lib/images/section2/img1.jpg';
	import image5 from '$lib/images/section2/img2.jpg';
	import image6 from '$lib/images/section2/img3.jpg';
	import image7 from '$lib/images/section3/img1.jpg';
	import image8 from '$lib/images/section3/img2.jpg';
	import image9 from '$lib/images/section3/img3.jpg';
	import image13 from '$lib/images/section4/img1.jpg';
	import image14 from '$lib/images/section4/img2.jpg';
	import image15 from '$lib/images/section4/img3.jpg';
	import image16 from '$lib/images/section5/img1.jpg';
	import image17 from '$lib/images/section5/img2.jpg';
	import image18 from '$lib/images/section5/img3.jpg';

	let container;
	let currentSection = 0;
	const totalSections = 5; // Total number of viewports

	let images = [];
	let transitionInProgress = false;

	function updateImagesAndLayout(vh, vw, mouseX, mouseY) {
		if (transitionInProgress) return;
		transitionInProgress = true;
		const nextSection = (currentSection + 1) % totalSections;
		let nextImages = [];

		if (nextSection === 0) {
			// Next viewport: array of images for section 0
			nextImages = [image1, image2, image3];
			updateLayoutForSection1(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 1) {
			// Next viewport: array of images for section 1
			nextImages = [image4, image5, image6];
			updateLayoutForSection2(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 2) {
			// Next viewport: array of images for section 2
			nextImages = [image7, image8, image9];
			updateLayoutForSection3(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 3) {
			// Next viewport: array of images for section 3
			nextImages = [image13, image14, image15];
			updateLayoutForSection4(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 4) {
			// Next viewport: array of images for section 4
			nextImages = [image16, image17, image18];
			updateLayoutForSection5(vh, vw, mouseX, mouseY, nextImages);
		}

		transitionImages(vh, vw, images, nextImages);
		currentSection = nextSection;
	}

	function updateLayoutForSection1(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 0
		nextImages.forEach((image, index) => {
			const angle = ((Math.PI * 2) / nextImages.length) * index;
			const size = 300; // Size of the square
			const x = vw / 2 + size * Math.cos(angle) - 25;
			const y = vh / 2 + size * Math.sin(angle) - 25;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection2(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 1
		nextImages.forEach((image, index) => {
			const angle = ((Math.PI * 2) / nextImages.length) * index;
			const radius = 150; // Radius of the circle
			const x = vw / 2 + radius * Math.cos(angle) - 25;
			const y = vh / 2 + radius * Math.sin(angle) - 25;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection3(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 2
		nextImages.forEach((image, index) => {
			const x = mouseX + index * 160;
			const y = mouseY + index * 160;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection4(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 3
		nextImages.forEach((image, index) => {
			const x = mouseX - index * 160;
			const y = mouseY + index * 160;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection5(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 4
		const frameWidth = 1000; // Width of the frame
		const frameHeight = 300; // Height of the frame
		const x = mouseX - frameWidth / 2;
		const y = mouseY - frameHeight / 2;

		nextImages.forEach((image, index) => {
			let imgX, imgY;

			if (index < 3) {
				// Top row
				imgX = x + index * (frameWidth / 3);
				imgY = y;
			} else {
				// Bottom row
				imgX = x + (index - 3) * (frameWidth / 3);
				imgY = y + frameHeight;
			}

			if (index >= nextImages.length) {
				// Handle cases where there are fewer images
				imgX = x + (index % 3) * (frameWidth / 3);
				imgY = y + Math.floor(index / 3) * (frameHeight / 2);
			}

			nextImages[index] = { src: image, x: imgX, y: imgY };
		});
	}

	function transitionImages(vh, vw, currentImages, nextImages) {
		const transitionDuration = 500; // Transition duration in milliseconds
		const startTime = Date.now();

		const updatePositions = () => {
			const currentTime = Date.now();
			const elapsedTime = currentTime - startTime;

			if (elapsedTime >= transitionDuration) {
				images = nextImages;
				transitionInProgress = false;
				return;
			}

			const progress = elapsedTime / transitionDuration;

			// Interpolate between current and next positions for each image
			images = currentImages.map((currentImg, index) => {
				const nextImg = nextImages[index];
				const x = currentImg.x + (nextImg.x - currentImg.x) * progress;
				const y = currentImg.y + (nextImg.y - currentImg.y) * progress;
				return { src: currentImg.src, x, y };
			});

			requestAnimationFrame(updatePositions);
		};

		requestAnimationFrame(updatePositions);
	}

	function createExplosion(x, y) {
		const explosionElement = document.createElement('div');
		explosionElement.className = 'explosion';
		explosionElement.style.left = `${x}px`;
		explosionElement.style.top = `${y}px`;
		document.body.appendChild(explosionElement);

		// After a short delay, remove the explosion element
		setTimeout(() => {
			explosionElement.remove();
		}, 1000);
	}

	onMount(() => {
		const vh = window.innerHeight;
		const vw = window.innerWidth;

		function handleMouseMove(event) {
			const mouseX = event.clientX;
			const mouseY = event.clientY;
			updateImagesAndLayout(vh, vw, mouseX, mouseY);
		}

		function handleClick(event) {
			if (!transitionInProgress) {
				updateImagesAndLayout(vh, vw, event.clientX, event.clientY);
				createExplosion(event.clientX, event.clientY);
			}
		}

		document.body.addEventListener('mousemove', handleMouseMove);
		document.body.addEventListener('click', handleClick);

		return () => {
			document.body.removeEventListener('mousemove', handleMouseMove);
			document.body.removeEventListener('click', handleClick);
		};
	});
</script>

<div bind:this={container} class="container">
	{#each images as { src, x, y }, index}
		<img {src} alt={`Image ${index}`} class="image" style="top: {y}px; left: {x}px;" />
	{/each}

	<h1>Originals</h1>

	<img class="logo" src="logo.svg" alt="" />
</div>

<style>
	.image {
		position: absolute;
		width: 400px;
		z-index: 2;
	}
	.container {
		height: 100vh;
		position: relative;
		width: 100vw;
		background-color: #f7f7f7;
	}

	.logo {
		position: fixed;
		bottom: 0;
		left: 50%;
		transform: translateX(-50%);
		padding: 24px;
		width: 140px;
	}

	.explosion {
		position: absolute;
		width: 50px;
		height: 50px;
		background-image: url('🌼');
		background-size: cover;
		animation: explode 1s ease-out;
	}
	@keyframes explode {
		0% {
			transform: scale(0);
			opacity: 1;
		}
		100% {
			transform: scale(2);
			opacity: 0;
		}
	}

	h1 {
		position: fixed;
		top: 0;
		z-index: 1;
		font-size: 460px;
		text-transform: uppercase;
		color: black;
		left: 50%;
		transform: translateX(-50%);
		padding: 16px;
	}
</style>
