<script lang="ts">
	import { page } from '$app/state';
	import { onMount } from 'svelte';
	import { Client } from 'tmi.js';

	import WordCloud from '$lib/components/WordCloud.svelte';

	let words: string[] = $state([]);

	let debug = $state(false);
	let logMessage = $state('');

	onMount(() => {
		const { searchParams } = page.url;
		const channelName = searchParams.get('channelName');
		const maxWords = parseInt(searchParams.get('maxWords') || '100');

		debug = searchParams.get('debug') !== null;

		if (!channelName) {
			logMessage += 'Channel name is required\n';

			return;
		}

		const client = new Client({
			channels: [channelName]
		});

		client.connect();

		client.on('connected', (address, port) => {
			logMessage += `Connected to ${address}:${port}\n`;
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

		client.on('disconnected', () => {
			logMessage += 'Disconnected from Twitch\n';
		});

		client.on('reconnect', () => {
			logMessage += 'Reconnecting to Twitch...\n';
		});

		return () => {
			client.disconnect();
		};
	});

	$effect(() => {
		console.log('Log message:', logMessage);
	});
</script>

{#if debug && logMessage}
	<div
		class="absolute top-0 left-0 z-100 flex h-screen w-screen flex-col items-center justify-center font-bold whitespace-pre-wrap text-red-600"
	>
		<p>{logMessage}</p>
		<p>Words amount: {words.length}</p>
	</div>
{/if}

<WordCloud {words} />
