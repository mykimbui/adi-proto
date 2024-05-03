<script>
	import { onMount } from 'svelte';

	let container;
	let currentSection = 0;
	const totalSections = 5; // Total number of viewports
	let scrolling = false; // Flag to prevent rapid scroll effects

	let rects = Array.from({ length: 11 }, (_, i) => ({ x: 0, y: 0 }));

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

	function updatePositions(vh, vw) {
		if (currentSection === 0) {
			// First viewport: rotating around a square
			rects = rects.map((_, index) => {
				const angle = ((Math.PI * 2) / rects.length) * index;
				const size = 300; // Size of the square
				return {
					x: size * Math.cos(angle) + vw / 2 - 25,
					y: size * Math.sin(angle) + vh / 2 - 25
				};
			});
		} else if (currentSection === 1) {
			// Second viewport: rotating around a circle
			rects = rects.map((_, index) => {
				const angle = ((Math.PI * 2) / rects.length) * index;
				const radius = 150; // Radius of the circle
				return {
					x: radius * Math.cos(angle) + vw / 2 - 25,
					y: radius * Math.sin(angle) + vh * 1.5 - 25
				};
			});
		} else if (currentSection === 2) {
			// Third viewport: align in a diagonal line
			rects = rects.map((_, index) => {
				return {
					x: index * 80 + vw / 4,
					y: index * 80 + vh * 2
				};
			});
		} else if (currentSection === 3) {
			// Fourth viewport: mirrored diagonal line
			rects = rects.map((_, index) => {
				return {
					x: vw - (index * 80 + vw / 4),
					y: index * 80 + vh * 3
				};
			});
		} else if (currentSection === 4) {
			// Fifth viewport: forming a frame
			const frameWidth = 500; // Width of the frame
			const frameHeight = 300; // Height of the frame
			const xOffset = (vw - frameWidth) / 2; // Centering frame horizontally
			const yOffset = vh * 4 + (vh - frameHeight) / 2; // Centering frame vertically in the fifth viewport
			rects = rects.map((_, index) => {
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
					// Place any additional rectangles appropriately
					x = xOffset + frameWidth / 2;
					y = yOffset + frameHeight / 2;
				}
				return { x, y };
			});
		}
	}

	onMount(() => {
		const vh = window.innerHeight;
		const vw = window.innerWidth;
		updatePositions(vh, vw); // Initial update

		function handleScroll(event) {
			if (scrolling) return; // Ignore scrolls while animating
			event.preventDefault();
			const delta = event.deltaY;

			if ((delta > 0 && currentSection < totalSections - 1) || (delta < 0 && currentSection > 0)) {
				currentSection += delta > 0 ? 1 : -1;
				updatePositions(vh, vw);
				scrollToSection(currentSection);
			}
		}

		window.addEventListener('wheel', handleScroll, { passive: false });
		return () => window.removeEventListener('wheel', handleScroll);
	});
</script>

<div bind:this={container} class="container">
	{#each rects as rect}
		<div class="rectangle" style="transform: translate({rect.x}px, {rect.y}px);"></div>
	{/each}
	<div class="title">
		<h1>Originals</h1>
	</div>
</div>

<style>
	.rectangle {
		width: 50px;
		height: 80px;
		background-color: blue;
		position: absolute;
		transition: transform 0.5s ease;
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
