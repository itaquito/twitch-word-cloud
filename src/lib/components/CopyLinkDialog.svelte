<script lang="ts">
	import { Copy } from '@lucide/svelte';

	import { toast } from 'svelte-sonner';

	import * as Dialog from '$lib/components/ui/dialog';
	import { Input } from '$lib/components/ui/input';
	import { Button } from '$lib/components/ui/button';

	interface Props {
		isModalOpen: boolean;
		parameters: {
			channelName: string;
			width: number;
			height: number;
			maxWords: number;
			interval: number;
			minAppearances: number;
		};
	}

	let { isModalOpen = $bindable(false), parameters }: Props = $props();

	let link = $derived.by(() => {
		if (typeof window === 'undefined') return '';

		const url = new URL('/overlay', window.location.origin);

		url.searchParams.set('channelName', parameters.channelName.toLowerCase());
		url.searchParams.set('width', parameters.width.toString());
		url.searchParams.set('height', parameters.height.toString());
		url.searchParams.set('maxWords', parameters.maxWords.toString());
		url.searchParams.set('interval', parameters.interval.toString());
		url.searchParams.set('minAppearances', parameters.minAppearances.toString());

		return url.toString();
	});

	const handleCopyToClipboard = async () => {
		try {
			await navigator.clipboard.writeText(link);

			toast.success('Link copied to clipboard!');
		} catch (error) {
			console.error('Something went wrong while copying the link to the clipboard', error);

			toast.error('Something went wrong! Try to copy the link manually.');
		}
	};
</script>

<Dialog.Root bind:open={isModalOpen}>
	<Dialog.Content class="w-full text-left lg:w-1/2">
		<Dialog.Header>
			<Dialog.Title class="text-left">Here is your link</Dialog.Title>
			<Dialog.Description class="text-justify">
				You can use the generated link to embed the word cloud into your broadcast software as a
				browser source or just use it in a tab of your browser.
			</Dialog.Description>
		</Dialog.Header>

		<div class="flex gap-2">
			<Input class="w-full" value={link} readonly />

			<Button
				size="icon"
				variant="outline"
				onclick={handleCopyToClipboard}
				title="Copy link to clipboard"
			>
				<Copy />
			</Button>
		</div>
	</Dialog.Content>
</Dialog.Root>
