<script lang="ts" module>
	import { z } from 'zod';

	const FormSchema = z.object({
		channelName: z
			.string({ message: 'The Twitch channel name must be a string' })
			.nonempty({ message: 'The Twitch channel name is required' }),
		width: z
			.number({
				message: 'The width must be a number'
			})
			.min(100, { message: 'The width must be at least 100 pixels' }),
		height: z
			.number({
				message: 'The width must be a number'
			})
			.min(100, { message: 'The width must be at least 100 pixels' }),
		maxWords: z
			.number({ message: 'The maximum number of words must be a number' })
			.min(1, { message: 'The maximum number of words must be at least one' }),
		interval: z
			.number({ message: 'The update interval must be a number' })
			.min(1, { message: 'The update interval must be at least one millisecond' }),
		minAppearances: z
			.number({
				message: 'The minimum number of appearances must be a number'
			})
			.min(1, {
				message: 'The minimum number of appearances must be at least one'
			})
	});
</script>

<script lang="ts">
	import { superForm, defaults } from 'sveltekit-superforms';
	import { zod } from 'sveltekit-superforms/adapters';

	import * as Form from '$lib/components/ui/form';
	import { Input } from '$lib/components/ui/input';
	import CopyLinkDialog from '$lib/components/CopyLinkDialog.svelte';

	const form = superForm(
		defaults(
			{
				channelName: '',
				maxWords: 100,
				interval: 1000,
				width: 800,
				height: 800,
				minAppearances: 2
			},
			zod(FormSchema)
		),
		{
			SPA: true,
			validators: zod(FormSchema),
			resetForm: false,
			onUpdate: ({ form }) => {
				if (form.valid) {
					isModalOpen = true;
				}
			}
		}
	);
	const { form: formData, enhance } = form;

	let isModalOpen = $state(false);
</script>

<CopyLinkDialog bind:isModalOpen parameters={$formData} />

<form class="space-y-6" use:enhance>
	<Form.Field {form} name="channelName" class="w-full">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Twitch channel name</Form.Label>
				<Input {...props} bind:value={$formData.channelName} type="text" placeholder="itaquito" />
			{/snippet}
		</Form.Control>
		<Form.Description>The username of your Twitch channel.</Form.Description>
		<Form.FieldErrors />
	</Form.Field>

	<div class="flex space-x-6">
		<Form.Field {form} name="width" class="w-full">
			<Form.Control>
				{#snippet children({ props })}
					<Form.Label>Width</Form.Label>
					<Input
						{...props}
						bind:value={$formData.width}
						type="number"
						min={100}
						placeholder="800"
					/>
				{/snippet}
			</Form.Control>
			<Form.Description>The width of the word cloud in pixels.</Form.Description>
			<Form.FieldErrors />
		</Form.Field>

		<Form.Field {form} name="height" class="w-full">
			<Form.Control>
				{#snippet children({ props })}
					<Form.Label>Height</Form.Label>
					<Input
						{...props}
						bind:value={$formData.height}
						type="number"
						min={100}
						placeholder="800"
					/>
				{/snippet}
			</Form.Control>
			<Form.Description>The height of the word cloud in pixels.</Form.Description>
			<Form.FieldErrors />
		</Form.Field>
	</div>

	<Form.Field {form} name="maxWords" class="w-full">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Maximum words</Form.Label>
				<Input {...props} bind:value={$formData.maxWords} type="number" min={1} placeholder="100" />
			{/snippet}
		</Form.Control>
		<Form.Description>
			The maximum number of words that will be displayed at the word cloud. Example: If it is set to
			100, the latest 100 words sent on your chat will be used to generate the word cloud.
		</Form.Description>
		<Form.FieldErrors />
	</Form.Field>

	<Form.Field {form} name="interval" class="w-full">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Update interval</Form.Label>
				<Input
					{...props}
					bind:value={$formData.interval}
					type="number"
					min={1}
					placeholder="1000"
				/>
			{/snippet}
		</Form.Control>
		<Form.Description>The update interval of the word cloud in milliseconds.</Form.Description>
		<Form.FieldErrors />
	</Form.Field>

	<Form.Field {form} name="minAppearances" class="w-full">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Minimum number of appearances</Form.Label>
				<Input
					{...props}
					bind:value={$formData.minAppearances}
					type="number"
					min={1}
					placeholder="2"
				/>
			{/snippet}
		</Form.Control>
		<Form.Description>
			The minimum number of appearances a word must have in order to appear on the word cloud.
		</Form.Description>
		<Form.FieldErrors />
	</Form.Field>

	<Form.Button class="cursor-pointer">Generate link</Form.Button>
</form>
