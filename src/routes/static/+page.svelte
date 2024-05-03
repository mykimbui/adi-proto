<script>
	import { onMount } from 'svelte';

	let container;
	let currentSection = 0;
	const totalSections = 5; // Total number of viewports
	let scrolling = false; // Flag to prevent rapid scroll effects

	let images = [];

	function scrollToSection(section) {
		const targetY = window.innerHeight * section;
		function scrollStep() {
			const currentY = window.scrollY;
			const distance = targetY - currentY;
			const step = distance > 0 ? Math.ceil(distance / 10) : Math.floor(distance / 10);

			if (Math.abs(distance) > 1) {
				window.scrollTo(0, currentY + step);
				requestAnimationFrame(scrollStep);
			} else {
				window.scrollTo(0, targetY); // Ensure it lands exactly on the target
				scrolling = false;
			}
		}

		scrolling = true;
		requestAnimationFrame(scrollStep);
	}

	function updateImagesAndLayout(vh, vw) {
		if (currentSection === 0) {
			// First viewport: array of images for section 0
			images = ['image1.jpg', 'image2.jpg', 'image3.jpg', 'image4.jpg', 'image5.jpg'];
			updateLayoutForSection1(vh, vw);
		} else if (currentSection === 1) {
			// Second viewport: array of images for section 1
			images = ['image6.jpg', 'image7.jpg', 'image8.jpg', 'image9.jpg', 'image10.jpg'];
			updateLayoutForSection2(vh, vw);
		} else if (currentSection === 2) {
			// Third viewport: array of images for section 2
			images = ['image11.jpg', 'image12.jpg', 'image13.jpg', 'image14.jpg', 'image15.jpg'];
			updateLayoutForSection3(vh, vw);
		} else if (currentSection === 3) {
			// Fourth viewport: array of images for section 3
			images = ['image16.jpg', 'image17.jpg', 'image18.jpg', 'image19.jpg', 'image20.jpg'];
			updateLayoutForSection4(vh, vw);
		} else if (currentSection === 4) {
			// Fifth viewport: array of images for section 4
			images = ['image21.jpg', 'image22.jpg', 'image23.jpg', 'image24.jpg', 'image25.jpg'];
			updateLayoutForSection5(vh, vw);
		}
	}

	function updateLayoutForSection1(vh, vw) {
		// First viewport layout update
		// Example layout: rotating around a square
		images.forEach((image, index) => {
			const angle = ((Math.PI * 2) / images.length) * index;
			const size = 500; // Size of the square
			const x = size * Math.cos(angle) + vw / 2 - 25;
			const y = size * Math.sin(angle) + vh / 2 - 25;
			images[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection2(vh, vw) {
		// Second viewport layout update
		// Example layout: rotating around a circle
		images.forEach((image, index) => {
			const angle = ((Math.PI * 2) / images.length) * index;
			const radius = 300; // Radius of the circle
			const x = radius * Math.cos(angle) + vw / 2 - 25;
			const y = radius * Math.sin(angle) + vh * 1.5 - 25;
			images[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection3(vh, vw) {
		// Third viewport layout update
		// Example layout: align in a diagonal line
		images.forEach((image, index) => {
			const x = index * 160 + vw / 4;
			const y = index * 160 + vh * 2;
			images[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection4(vh, vw) {
		// Fourth viewport layout update
		// Example layout: mirrored diagonal line
		images.forEach((image, index) => {
			const x = vw - (index * 200 + vw / 4);
			const y = index * 200 + vh * 4;
			images[index] = { src: image, x, y };
		});
	}

	function updateLayoutForSection5(vh, vw) {
		// Fifth viewport layout update
		// Example layout: forming a frame
		const frameWidth = 1400; // Width of the frame
		const frameHeight = 300; // Height of the frame
		const xOffset = (vw - frameWidth) / 2; // Centering frame horizontally
		const yOffset = vh * 4 + (vh - frameHeight) / 2; // Centering frame vertically in the fifth viewport
		images.forEach((image, index) => {
			let x, y;
			if (index < 5) {
				// Top row
				x = xOffset + index * (frameWidth / 5);
				y = yOffset;
			} else if (index < 10) {
				// Bottom row
				x = xOffset + (index - 5) * (frameWidth / 5);
				y = yOffset + frameHeight;
			} else {
				// Place any additional images appropriately
				x = xOffset + frameWidth / 2;
				y = yOffset + frameHeight / 2;
			}
			images[index] = { src: image, x, y };
		});
	}

	onMount(() => {
		const vh = window.innerHeight;
		const vw = window.innerWidth;
		updateImagesAndLayout(vh, vw); // Initial update

		function handleScroll(event) {
			if (scrolling) return; // Ignore scrolls while animating
			event.preventDefault();
			const delta = event.deltaY;

			if ((delta > 0 && currentSection < totalSections - 1) || (delta < 0 && currentSection > 0)) {
				currentSection += delta > 0 ? 1 : -1;
				updateImagesAndLayout(vh, vw);
				scrollToSection(currentSection);
			}
		}

		window.addEventListener('wheel', handleScroll, { passive: false });
		return () => window.removeEventListener('wheel', handleScroll);
	});
</script>

<div bind:this={container} class="container">
	{#each images as { src, x, y }, index}
		<img {src} alt={`Image ${index}`} class="image" style="transform: translate({x}px, {y}px);" />
	{/each}
	<div class="title">
		<h1>Originals</h1>
	</div>
</div>

<style>
	.image {
		position: absolute;
		transition: transform 0.5s ease;
		width: 200px;
		height: auto;
	}
	.container {
		height: 500vh;
		position: relative;
		width: 100vw;
		background-color: black;
	}
	h1 {
		text-transform: uppercase;
	}
</style>
