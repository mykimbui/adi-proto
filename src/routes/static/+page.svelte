<script>
	import { onMount, onDestroy } from 'svelte';

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
			nextImages = ['image1.jpg', 'image2.jpg', 'image3.jpg', 'image4.jpg', 'image5.jpg'];
			updateLayoutForSection1(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 1) {
			// Next viewport: array of images for section 1
			nextImages = ['image6.jpg', 'image7.jpg', 'image8.jpg', 'image9.jpg', 'image10.jpg'];
			updateLayoutForSection2(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 2) {
			// Next viewport: array of images for section 2
			nextImages = ['image11.jpg', 'image12.jpg', 'image13.jpg', 'image14.jpg', 'image15.jpg'];
			updateLayoutForSection3(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 3) {
			// Next viewport: array of images for section 3
			nextImages = ['image16.jpg', 'image17.jpg', 'image18.jpg', 'image19.jpg', 'image20.jpg'];
			updateLayoutForSection4(vh, vw, mouseX, mouseY, nextImages);
		} else if (nextSection === 4) {
			// Next viewport: array of images for section 4
			nextImages = ['image21.jpg', 'image22.jpg', 'image23.jpg', 'image24.jpg', 'image25.jpg'];
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
			const x = mouseX + index * 80;
			const y = mouseY + index * 80;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection4(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 3
		nextImages.forEach((image, index) => {
			const x = mouseX - index * 80;
			const y = mouseY + index * 80;
			nextImages[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection5(vh, vw, mouseX, mouseY, nextImages) {
		// Calculate next layout for section 4
		const frameWidth = 500; // Width of the frame
		const frameHeight = 300; // Height of the frame
		const x = mouseX - frameWidth / 2;
		const y = mouseY - frameHeight / 2;
		nextImages.forEach((image, index) => {
			let imgX, imgY;
			if (index < 5) {
				// Top row
				imgX = x + index * (frameWidth / 5);
				imgY = y;
			} else if (index < 10) {
				// Bottom row
				imgX = x + (index - 5) * (frameWidth / 5);
				imgY = y + frameHeight;
			} else {
				// Place any additional images appropriately
				imgX = x + frameWidth / 2;
				imgY = y + frameHeight / 2;
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

	onMount(() => {
		const vh = window.innerHeight;
		const vw = window.innerWidth;
		const body = document.querySelector('body');

		function handleMouseMove(event) {
			const mouseX = event.clientX;
			const mouseY = event.clientY;
			updateImagesAndLayout(vh, vw, mouseX, mouseY);
		}

		function handleClick(event) {
			if (!transitionInProgress) {
				updateImagesAndLayout(vh, vw, event.clientX, event.clientY);
			}
		}

		body.addEventListener('mousemove', handleMouseMove);
		body.addEventListener('click', handleClick);

		return () => {
			body.removeEventListener('mousemove', handleMouseMove);
			body.removeEventListener('click', handleClick);
		};
	});
</script>

<div bind:this={container} class="container">
	{#each images as { src, x, y }, index}
		<img {src} alt={`Image ${index}`} class="image" style="top: {y}px; left: {x}px;" />
	{/each}
	<!-- <div class="title">
		<h1>Originals</h1>
	</div> -->
</div>

<style>
	.image {
		position: absolute;
		/* Additional styling for images */
		width: 200px;
	}
	.container {
		height: 100vh;
		position: relative;
		width: 100vw;
		background-color: black;
	}
	h1 {
		text-transform: uppercase;
		color: white;
	}
</style>
