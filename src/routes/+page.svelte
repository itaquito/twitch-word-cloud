<script lang="ts">
	import * as Card from '$lib/components/ui/card';
	import * as Alert from '$lib/components/ui/alert';

	import CreateLinkForm from '$lib/components/CreateLinkForm.svelte';
</script>

<div class="flex min-h-screen w-screen items-center justify-center bg-slate-100">
	<Card.Root class="m-4 w-full lg:w-1/2">
		<Card.Header>
			<Card.Title>Twitch Word Cloud</Card.Title>
			<Card.Description>
				Create a word cloud that updates in real time based on your Twitch chat.
				<br />
				You can use the generated link to embed the word cloud into your broadcast software as a browser
				source or just use it in your browser.
			</Card.Description>
		</Card.Header>
		<Card.Content>
			<CreateLinkForm />
		</Card.Content>
	</Card.Root>
</div>
