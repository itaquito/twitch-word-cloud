<script lang="ts">
	import { page } from '$app/state';
	import { onMount } from 'svelte';
	import { Client } from 'tmi.js';

	let debug = $state(false);
	let errorMessage = $state('');

	onMount(() => {
		const { searchParams } = page.url;
		const channelName = searchParams.get('channel');

		debug = searchParams.get('debug') !== null;

		if (!channelName) {
			errorMessage += 'Channel name is required';
			return console.error(errorMessage);
		}

		const client = new Client({
			channels: [channelName]
		});

		client.connect();

		client.on('connected', (address, port) => {
			console.log(`Connected to ${address}:${port}`);
		});

		client.on('message', (_, tags, message) => {
			console.log(`${tags['display-name']}: ${message}`);
		});

		return () => {
			client.disconnect();
		};
	});
</script>

{#if debug && errorMessage}
	<div class="flex h-screen w-screen items-center justify-center font-bold text-red-600">
		<p>{errorMessage}</p>
	</div>
{/if}
