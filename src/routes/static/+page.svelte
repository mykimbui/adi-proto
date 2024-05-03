<script>
	import { onMount } from 'svelte';

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
			nextImages = ['image1.jpg', 'image2.jpg', 'image3.jpg'];
			updateLayoutForSection1(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 1) {
			// Next viewport: array of images for section 1
			nextImages = ['image6.jpg', 'image7.jpg', 'image8.jpg'];
			updateLayoutForSection2(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 2) {
			// Next viewport: array of images for section 2
			nextImages = ['image11.jpg', 'image12.jpg', 'image13.jpg'];
			updateLayoutForSection3(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 3) {
			// Next viewport: array of images for section 3
			nextImages = ['image16.jpg', 'image17.jpg', 'image18.jpg'];
			updateLayoutForSection4(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 4) {
			// Next viewport: array of images for section 4
			nextImages = ['image21.jpg', 'image22.jpg', 'image23.jpg'];
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
		width: 300px;
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
		font-size: 380px;
		text-transform: uppercase;
		color: black;
		left: 50%;
		transform: translateX(-50%);
		padding: 16px;
	}
</style>
