<script lang="ts">
	import '../app.css';

	import { Toaster } from '$lib/components/ui/sonner';

	let { children } = $props();
</script>

<svelte:head>
	<title>Twitch Word Cloud</title>
</svelte:head>

<Toaster />

{@render children()}
