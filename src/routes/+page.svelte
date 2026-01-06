<script lang="ts">
	import { fade } from 'svelte/transition';
	import { base } from '$app/paths';

	let opened = false;
    let showMessage = false;


	let showCake = false;
	let showSmoke = false;
	let frame = 1;

	const totalFrames = 6;

	const frameMs = 150;

	let intervalId: ReturnType<typeof setInterval> | null = null;

	function handleOpen() {
		if (opened) return;
		opened = true;

		showSmoke = true;
		showCake = false; // ✅ cake starts hidden
		frame = 1;

		intervalId = setInterval(() => {
			frame += 1;

			// ✅ reveal cake mid-animation
			if (frame === 3) {
				showCake = true;

                setTimeout(() => {
		            showMessage = true;
	            }, 250);
			}

			if (frame > totalFrames) {
				if (intervalId) clearInterval(intervalId);
				intervalId = null;

				showSmoke = false; 
				showCake = true;   
                showMessage = true;
			}
		}, frameMs);
	}
</script>

<svelte:head>
	<title></title>
</svelte:head>

<main class="wrap">
	<div class="stage">
		{#if showCake}
			<img
				class="cake"
				src={`${base}/assets/lemondrizzle.png`}
				alt="Pixel lemon drizzle cake"
				draggable="false"
			/>
		{/if}

        {#if showMessage}
	        <p class="message" in:fade={{ duration: 220 }}>
		        Here&apos;s your lemon drizzle cake :) happy birthday!
	        </p>
        {/if}

		{#if showSmoke}
			<img
				class="smoke"
				src={`${base}/assets/smoke_${frame}.png`}
				alt=""
				aria-hidden="true"
				draggable="false"
				in:fade={{ duration: 40 }}
				out:fade={{ duration: 180 }}
			/>
		{/if}

		{#if !opened}
			<button class="giftButton" on:click={handleOpen} aria-label="Open the birthday gift">
				<img
					class="gift"
					src={`${base}/assets/birthdaybox.png`}
					alt="Pixel birthday gift box"
					draggable="false"
				/>
				<div class="hint">click me</div>
			</button>
		{/if}
	</div>
</main>

<style>
	.wrap {
		min-height: 100vh;
		display: grid;
		place-items: center;
		padding: 18px;
		background: #f3d6b4;
	}

	.stage {
  display: flex;
  flex-direction: column;
  align-items: center;
}



	.giftButton {
		all: unset;
		display: grid;
		place-items: center;
		gap: 10px;
		cursor: pointer;
		text-align: center;
		-webkit-tap-highlight-color: transparent;
		user-select: none;
		position: relative;
		z-index: 3;
	}

	.gift {
		width: min(320px, 80vw);
		height: auto;
		image-rendering: pixelated;
		image-rendering: crisp-edges;
		filter: drop-shadow(0 10px 16px rgba(0, 0, 0, 0.18));
		transform-origin: 50% 80%;
		animation: nudge 1.2s ease-in-out infinite;
	}

	.hint {
		font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New',
			monospace;
		font-size: 13px;
		letter-spacing: 0.06em;
		color: rgba(0, 0, 0, 0.65);
		text-transform: lowercase;
	}

    .message {
	    margin-top: 350px;
	    max-width: 320px;
	    text-align: center;
	    font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
		    'Liberation Mono', 'Courier New', monospace;
	    font-size: 13px;
	    letter-spacing: 0.04em;
	    line-height: 1.4;
	    color: rgba(0, 0, 0, 0.7);
    }

	.cake {
		position: absolute;
		inset: 0;
		margin: auto;
		width: min(380px, 86vw);
		height: auto;
		image-rendering: pixelated;
		image-rendering: crisp-edges;
		filter: drop-shadow(0 10px 16px rgba(0, 0, 0, 0.16));
		z-index: 1;
		pointer-events: none;
	}

	.smoke {
		position: absolute;
		inset: 0;
		margin: auto;
		width: min(420px, 92vw);
		height: auto;
		image-rendering: pixelated;
		image-rendering: crisp-edges;
		z-index: 2;
		pointer-events: none;
	}

	@keyframes nudge {
		0% { transform: translateY(0) rotate(0deg); }
		55% { transform: translateY(-2px) rotate(-1.2deg); }
		65% { transform: translateY(-2px) rotate(1.2deg); }
		75% { transform: translateY(-1px) rotate(-0.8deg); }
		85% { transform: translateY(-1px) rotate(0.8deg); }
		100% { transform: translateY(0) rotate(0deg); }
	}

	@media (prefers-reduced-motion: reduce) {
		.gift { animation: none; }
	}
</style>
