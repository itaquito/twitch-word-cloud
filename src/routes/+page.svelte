<script lang="ts">
	import * as Card from '$lib/components/ui/card';

	import CreateLinkForm from '$lib/components/CreateLinkForm.svelte';
</script>

<div class="flex min-h-screen w-screen items-center justify-center bg-slate-100">
	<Card.Root class="m-4 w-full lg:w-1/2">
		<Card.Header>
			<Card.Title>Twitch Word Cloud</Card.Title>
			<Card.Description class="text-justify">
				Create a word cloud that updates in real time based on your Twitch chat.
			</Card.Description>
		</Card.Header>
		<Card.Content>
			<CreateLinkForm />
		</Card.Content>
	</Card.Root>
</div>
