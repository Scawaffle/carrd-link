<script>
	import { onMount } from 'svelte';
	import {
		Code2,
		Send,
		Headphones,
		Globe2,
		Cloud,
		Brush,
		Focus,
		Bird,
		Archive
	} from '@lucide/svelte';
	import { siSpotify, siSoundcloud } from 'simple-icons';

	import { track } from '@vercel/analytics';

	let isOpen = $state(false);
	let dotsPaused = $state(false);
	let dotResumeTimer;
	let reopenTimer;
	const AUTO_OPEN_DELAY = 1_000;
	const REOPEN_DELAY = 2_500;
	const DOT_RESUME_DELAY = 10;

	function toggleCard() {
		window.clearTimeout(reopenTimer);
		isOpen = !isOpen;
	}

	function closeCard() {
		isOpen = false;
		window.clearTimeout(reopenTimer);
		reopenTimer = window.setTimeout(() => {
			isOpen = true;
		}, REOPEN_DELAY);
	}

	function pauseDotsForCardTransition(event) {
		if (event.target !== event.currentTarget || !['width', 'height'].includes(event.propertyName)) return;
		if (!window.matchMedia('(max-width: 40rem)').matches) return;

		dotsPaused = true;
		window.clearTimeout(dotResumeTimer);
	}

	function resumeDotsAfterCardTransition(event) {
		if (event.target !== event.currentTarget || !['width', 'height'].includes(event.propertyName)) return;
		if (!window.matchMedia('(max-width: 40rem)').matches) return;

		window.clearTimeout(dotResumeTimer);
		dotResumeTimer = window.setTimeout(() => {
			dotsPaused = false;
		}, DOT_RESUME_DELAY);
	}

	function keepDotsMovingOnDesktop() {
		if (!window.matchMedia('(max-width: 40rem)').matches) {
			window.clearTimeout(dotResumeTimer);
			dotsPaused = false;
		}
	}

	onMount(() => {
		let openTimer;

		// The window load event means images and other page assets have finished loading.
		function startAutoOpenTimer() {
			openTimer = window.setTimeout(() => {
				isOpen = true;
			}, AUTO_OPEN_DELAY);
		}

		if (document.readyState === 'complete') {
			startAutoOpenTimer();
		} else {
			window.addEventListener('load', startAutoOpenTimer, { once: true });
		}

		const mobileQuery = window.matchMedia('(max-width: 40rem)');
		mobileQuery.addEventListener('change', keepDotsMovingOnDesktop);

		return () => {
			window.clearTimeout(openTimer);
			window.clearTimeout(reopenTimer);
			window.clearTimeout(dotResumeTimer);
			window.removeEventListener('load', startAutoOpenTimer);
			mobileQuery.removeEventListener('change', keepDotsMovingOnDesktop);
		};
	});
</script>

<svelte:head>
	<title>Waffle's Carrd</title>
	<meta
		name="description"
		content="Waffle's carrd - I dunno what to put here honestly other than links."
	/>
</svelte:head>

<main>
	<div class="dot-background" class:paused={dotsPaused} aria-hidden="true"></div>

	<section
		class="card-shell"
		class:open={isOpen}
		aria-label="Scawaffle profile card"
		ontransitionrun={pauseDotsForCardTransition}
		ontransitionend={resumeDotsAfterCardTransition}
	>
		<button
			class="avatar-button"
			type="button"
			aria-label={isOpen ? "Scawaffle's fursona" : 'Open profile card'}
			aria-expanded={isOpen}
			aria-controls="card-content"
			onclick={toggleCard}
		>
			<img class="avatar-image" src="/images/scawaffle.png" alt="Scawaffle's fursona" />
		</button>

		<aside class="avatar-status" aria-label="Currently">
			<p class="status-label">Currently</p>
			<p class="status-description">Post processing &amp; sorting shots from PAWAI. <br />🦊💻</p>
			<p class="status-description">One pic at a time with coffee &amp; waffles aside<br />🧇☕</p>
			<hr class="status-divider" />
			<p class="status-description">Do people even read this tho? 🤔</p>
		</aside>

		{#if isOpen}
			<button class="close-button" type="button" onclick={closeCard}>
				<span aria-hidden="true">&times;</span>
				<span class="visually-hidden">Close profile card</span>
			</button>
		{/if}

		<div id="card-content" class="card-content" aria-hidden={!isOpen} inert={!isOpen}>
			<div class="card-main">
				<div class="introduction">
					<p class="eyebrow">Hello, I'm</p>
					<h1>Scawaffle <span class="nickname">(waffle)</span></h1>
					<p class="tagline">
						Civil Eng. | BIM | Code | ongoing furry VN Dev.
					</p>
					<p><small>(mostly figuring it out as I go)</small></p>
				</div>

				<nav class="links" aria-label="Scawaffle's links">
					<a
						href="https://bsky.app/profile/scawaffle.me"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'bluesky' })}
					>
						<Cloud size={17} strokeWidth={1.8} aria-hidden="true" />
						<span>Bluesky <small>(hacked atm)</small> </span>
					</a>

					<a
						href="https://github.com/Scawaffle"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'github' })}
					>
						<Code2 size={17} strokeWidth={1.8} aria-hidden="true" />
						<span>GitHub</span>
					</a>

					<a
						href="https://www.instagram.com/scawaffle/"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'instagram' })}
					>
						<Focus size={17} strokeWidth={1.8} aria-hidden="true" />
						<span>Instagram</span>
					</a>

					<a
						href="https://t.me/Scawaffle"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'telegram' })}
					>
						<Send size={17} strokeWidth={1.8} aria-hidden="true" />
						<span>Telegram</span>
					</a>

					<a
						href="https://t.me/+kiPHtyrQiLMyODA9"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'art_channel' })}
					>
						<Brush size={17} strokeWidth={1.8} aria-hidden="true" />
						<span>Art Channel</span>
					</a>

					<a
						href="https://soundcloud.com/scawafflez"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'soundcloud' })}
					>
						<svg
							width="18"
							height="18"
							viewBox="0 0 24 24"
							fill="currentColor"
							aria-hidden="true"
						>
							<path d={siSoundcloud.path} />
						</svg>
						<span>SoundCloud</span>
					</a>

					<a
						href="https://twitter.com/scawaffle"
						target="_blank"
						rel="noopener noreferrer"
						class="crossed-button"
						onclick={() => track('link_click', { link: 'twitter' })}
					>
						<Bird size={17} strokeWidth={1.8} aria-hidden="true" />
						<span><span class="crossed">Twitter / X</span> - 🚮</span>
					</a>

					<a
						href="https://open.spotify.com/user/w79bjjmaq7jcg6upilhcqmubn"
						target="_blank"
						rel="noopener noreferrer"
						onclick={() => track('link_click', { link: 'spotify' })}
					>
						<svg
							width="17"
							height="17"
							viewBox="0 0 24 24"
							fill="currentColor"
							aria-hidden="true"
						>
							<path d={siSpotify.path} />
						</svg>
						<span>Spotify</span>
					</a>

					<a
						href="https://drive.google.com/drive/folders/1gvtuoFbeRPV15ila8u4H2fows-bh71W_?usp=drive_link"
						target="_blank"
						rel="noopener noreferrer"
						class="wide"
						onclick={() => track('link_click', { link: 'pawai_archive' })}
					>
						<Archive size={15} strokeWidth={1.8} aria-hidden="true" />
						<span>PAWAI 2026 Pic Archive</span>
					</a>

					<button class="coming-soon" type="button" disabled>
						<Globe2 size={15} strokeWidth={1.8} aria-hidden="true" />
						<span>Main site - under construction</span>
					</button>
				</nav>
			</div>

			<footer class="copyright">© {new Date().getFullYear()} Scawaffle <br>Art ©hiroosy</footer>
		</div>
	</section>
</main>

<style>
	:global(*) {
		box-sizing: border-box;
	}

	:global(html, body) {
		margin: 0;
		min-height: 100%;
	}

	:global(body) {
		font-family:
			Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
	}

	main {
		position: relative;
		min-height: 100vh;
		min-height: 100svh;
		display: grid;
		place-items: center;
		overflow: hidden;
		padding: 1.5rem;
		background: #0b0f12;
	}

	.dot-background {
		position: absolute;
		inset: -4rem;
		pointer-events: none;
		background-image: radial-gradient(
			circle,
			rgba(255, 255, 255, 0.18) 1.75px,
			transparent 1.75px
		);
		background-size: 30px 30px;
		animation: dot-drift 8s linear infinite;
		will-change: transform;
	}

	.dot-background.paused {
		animation-play-state: paused;
	}

	@keyframes dot-drift {
		from {
			transform: translate3d(0, 0, 0);
		}

		to {
			transform: translate3d(60px, 60px, 0);
		}
	}

	.card-shell {
		position: relative;
		z-index: 1;
		width: 9rem;
		height: 9rem;
		overflow: hidden;
		border: 0.5rem solid #fffafa;
		border-radius: 50%;
		background: #f8f6ef;
		color: #172126;
		box-shadow: 0 1.5rem 4rem rgba(0, 0, 0, 0.35);
		transition:
			width 0.7s cubic-bezier(0.16, 1, 0.3, 1),
			height 0.7s cubic-bezier(0.16, 1, 0.3, 1),
			border-width 0.7s cubic-bezier(0.16, 1, 0.3, 1),
			border-color 0.4s ease,
			border-radius 0.7s cubic-bezier(0.16, 1, 0.3, 1);
	}

	.card-shell.open {
		width: min(90vw, 40rem);
		height: min(90vh, 42rem);
		border-width: 0.25rem;
		border-color: #ebce8f;
		border-radius: 1.3rem;
	}

	.avatar-button {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 2;
		width: 8rem;
		height: 8rem;
		padding: 0;
		border: 0;
		border-radius: 50%;
		background: transparent;
		box-shadow: 0 0 0 0.3rem rgba(237, 185, 82, 0.35);
		cursor: pointer;
		transition:
			top 0.7s cubic-bezier(0.16, 1, 0.3, 1),
			left 0.7s cubic-bezier(0.16, 1, 0.3, 1),
			transform 0.7s cubic-bezier(0.16, 1, 0.3, 1);
	}

	.avatar-image {
		width: 100%;
		height: 100%;
		display: block;
		object-fit: cover;
		border-radius: 50%;
		filter: grayscale(100%);
		transform: scale(1);
		transition:
			filter 0.7s ease,
			transform 0.7s cubic-bezier(0.16, 1, 0.3, 1);
	}

	.card-shell.open .avatar-button {
		top: 1.5rem;
		left: 1.5rem;
	}

	.card-shell.open .avatar-image {
		filter: grayscale(0%);
		transform: scale(1.08);
	}

	.avatar-status {
		position: absolute;
		top: 11rem;
		left: 1.5rem;
		width: 8rem;
		opacity: 0;
		transform: translateY(1rem);
		color: #4c565b;
		font-size: 0.72rem;
		line-height: 1.45;
		text-align: center;
		pointer-events: none;
		transition: opacity 0.3s ease, transform 0.4s ease;
	}

	.card-shell.open .avatar-status {
		opacity: 1;
		transform: translateY(0);
		pointer-events: auto;
		transition-delay: 0.45s;
	}

	.avatar-status p {
		margin: 0;
	}

	.avatar-status p + p {
		margin-top: 0.5rem;
	}

	.status-divider {
		width: 5rem;
		margin: 0.9rem auto;
		border: 0;
		border-top: 1px solid #ebce8f;
	}

	.status-label {
		margin-bottom: 0.35rem !important;
		color: #735b2d;
		font-size: 0.68rem;
		font-weight: 700;
		letter-spacing: 0.08em;
		text-transform: uppercase;
	}

	.status-description {
		margin: 0;
		font-size: 0.68rem;
		opacity: 1;
		transition:
			opacity 0.3s ease;
		transition-delay: 0.45s;	
	}

	.card-content {
		position: absolute;
		inset: 0;
		display: flex;
		flex-direction: column;
		justify-content: flex-start;
		padding: 4rem 3rem 4rem 11.5rem;
		overflow-y: auto;
		overscroll-behavior: contain;
		-webkit-overflow-scrolling: touch;
		opacity: 0;
		transform: translateY(1rem);
		pointer-events: none;
	}

	.card-shell.open .card-content {
		opacity: 1;
		transform: translateY(0);
		pointer-events: auto;
		transition:
			opacity 0.3s ease,
			transform 0.4s ease;
		transition-delay: 0.45s;
	}

	.card-main {
		width: 100%;
		margin-block: auto;
	}

	.copyright {
		position: relative;
		left: -4.25rem;
		margin-top: 1.5rem;
		color: #4c565b;
		font-size: 0.72rem;
		letter-spacing: 0.02em;
		text-align: center;
		opacity: 0;
		transform: translateY(0.35rem);
		transition:
			opacity 0.65s ease,
			transform 0.65s ease;
	}

	.card-shell.open .copyright {
		opacity: 0.58;
		transform: translateY(0);
		transition-delay: 1.05s;
	}

	.eyebrow, .tagline {
		margin: 0;
	}

	.introduction > p {
		margin: 0;
	}

	.introduction > p + p {
		margin-top: 0.2rem;
	}

	.eyebrow {
		color: #735b2d;
		font-size: 1rem;
		font-weight: 700;
		letter-spacing: 0.08em;
		text-transform: uppercase;
	}

	h1 {
		margin: 0.2rem 0 0.35rem;
		font-size: clamp(2.4rem, 8vw, 4.5rem);
		line-height: 0.95;
		letter-spacing: -0.055em;
	}

	.nickname {
		color: #735b2d;
		font-size: 0.32em;
		font-weight: 600;
		letter-spacing: 0;
		white-space: nowrap;
	}

	.tagline {
		color: #4c565b;
		font-size: 1.15rem;
	}

	.close-button {
		position: absolute;
		z-index: 3;
		top: 0.8rem;
		right: 0.9rem;
		width: 2.5rem;
		height: 2.5rem;
		border: 0;
		border-radius: 50%;
		background: transparent;
		color: inherit;
		font-size: 2rem;
		line-height: 1;
		cursor: pointer;
	}

	.close-button:hover,
	.close-button:focus-visible {
		background: rgba(23, 33, 38, 0.08);
	}

	.links {
		display: grid;
		grid-template-columns: repeat(2, minmax(0, 1fr));
		gap: 0.65rem;
		margin-top: 1.5rem;
	}

	.links a {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
		min-width: 0;
		min-height: 3rem;
		padding: 0.75rem 1.3rem;
		border: 0.125rem solid #1a1a1a;
		border-radius: 0.9rem;
		background: transparent;
		color: #292929;
		font: inherit;
		font-weight: 700;
		text-align: center;
		text-decoration: none;
		transition:
			background-color 0.2s ease,
			color 0.2s ease,
			transform 0.2s ease,
			box-shadow 0.2s ease;
	}

	.links a > span {
		min-width: 0;
	}

	.links a small {
		font-size: 0.6em;
		font-weight: 400;
		white-space: normal;
	}
	
	.links a:hover,
	.links a:focus-visible {
		background: #1a1a1a;
		color: #fff;
		box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.2);
		transform: translateY(-2px);
	}

	.avatar-button:focus-visible,
	.links a:focus-visible,
	.close-button:focus-visible {
		outline: 0.2rem solid #d68c1f;
		outline-offset: 0.2rem;
	}

	.visually-hidden {
		position: absolute;
		width: 1px;
		height: 1px;
		padding: 0;
		margin: -1px;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border: 0;
	}

	.introduction small {
		font-size: 0.7em;
		opacity: 0.75;
	}
	
	.crossed{
		text-decoration: line-through;
	}

	.crossed-button{
		border-style: dashed !important;
		opacity: 0.48;
	}

	.wide {
		grid-column: 1 / -1;	
		opacity: 1;
	}

	.coming-soon {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
		grid-column: 1 / -1;
		min-width: 0;
		min-height: 3rem;
		padding: 0.75rem 1.3rem;
		border: 0.125rem solid #1a1a1a;
		border-radius: 0.9rem;
		border-style: dashed !important;
		background: transparent;
		color: #292929;
		font: inherit;
		font-weight: 700;
		text-align: center;
		opacity: 0.48;
		cursor: not-allowed;
	}

	@media (max-width: 40rem) {
		.card-shell.open {
			width: min(90vw, 24rem);
			height: min(92vh, 41.5rem);
			overflow-x: hidden;
			overflow-y: auto;
			scrollbar-width: none;
			overscroll-behavior: contain;
		}
		.card-shell.open::-webkit-scrollbar {
			display: none;
		}
		.card-shell.open .avatar-button {
			top: 1.5rem;
			left: 50%;
			transform: translateX(-50%);
		}
		.avatar-status {
			display: none;
		}
		.card-content {
			position: relative;
			inset: auto;
			min-height: 100%;
			justify-content: flex-start;
			padding: 10.5rem 1.25rem 1.5rem;
			overflow-y: visible;
			text-align: center;
		}
		.card-main {
			margin-block: 0;
		}
		.copyright {
			left: 0;
			margin-top: 1.5rem;
		}
		.eyebrow {
			font-size: 0.75rem;
			letter-spacing: 0.07em;
		}
		h1 {
			margin: 0.15rem 0 0.3rem;
			font-size: clamp(2.2rem, 11vw, 2.8rem);
			line-height: 1;
		}
		.tagline {
			font-size: 0.9rem;
			line-height: 1.4;
		}
		.links {
			grid-template-columns: repeat(2, minmax(0, 1fr));
			gap: 0.55rem;
			width: 100%;
			margin-top: 1rem;
		}
		.links a {
			min-height: 2.5rem;
			padding: 0.5rem 0.55rem;
			border-width: 0.1rem;
			border-radius: 0.7rem;
			font-size: 0.82rem;
			line-height: 1.2;
		}
		.coming-soon {
			min-height: 2.5rem;
			padding: 0.5rem 0.55rem;
			border-width: 0.1rem;
			border-radius: 0.7rem;
			font-size: 0.82rem;
			line-height: 1.2;
		}
	}

	@media (prefers-reduced-motion: reduce) {
		.card-shell,
		.avatar-button,
		.avatar-image,
		.avatar-status,
		.card-shell.open .avatar-status,
		.card-content,
		.card-shell.open .card-content,
		.links a,
		.copyright {
			transition: none;
		}

		.dot-background {
			animation: none;
		}

		.card-shell.open .card-content {
			transition-delay: 0s;
		}

		.card-shell.open .copyright {
			transition-delay: 0s;
		}
	}
</style>
