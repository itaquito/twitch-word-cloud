<script lang="ts">
	import { page } from '$app/state';
	import { onMount } from 'svelte';
	import { Client } from 'tmi.js';

	import WordCloud from '$lib/components/WordCloud.svelte';

	let words: string[] = $state([]);

	let debug = $state(false);
	let errorMessage = $state('');

	onMount(() => {
		const { searchParams } = page.url;
		const channelName = searchParams.get('channel');
		const maxWords = parseInt(searchParams.get('words') || '100');

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
			const messageWords = message
				.toLowerCase()
				.replace(/[^a-zA-Z0-9\s]/g, '')
				.split(/\s+/)
				.filter((word) => word.length > 0 && word.length < 20);

			words.push(...messageWords);
			words = words.slice(-maxWords);
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

<WordCloud {words} />
