<script>
	import favicon from '$lib/assets/favicon.png';
	import { dev } from '$app/environment';
	import { injectAnalytics } from '@vercel/analytics/sveltekit';

	let { children } = $props();

	injectAnalytics({ mode: dev ? 'development' : 'production' });
</script>

<svelte:head>
	<link rel="icon" type="image/png" href={favicon} />
</svelte:head>

{@render children()}
