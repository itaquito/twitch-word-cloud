<script lang="ts">
	import '../app.css';

	let { children } = $props();
</script>

<svelte:head>
	<title>Twitch Word Cloud</title>
</svelte:head>

{@render children()}
