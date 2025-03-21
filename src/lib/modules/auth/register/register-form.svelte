<script lang="ts">
	import * as Form from '$lib/components/ui/form';
	import { Input } from '$lib/components/ui/input';
	import { defaults, superForm } from 'sveltekit-superforms';
	import { registerSchema, type RegisterInput } from '..';
	import { zod } from 'sveltekit-superforms/adapters';
	import { createMutation } from '@tanstack/svelte-query';
	import toast from 'svelte-french-toast';
	import axios from 'axios';

	const registerMutation = createMutation({
		mutationKey: ['register'],
		mutationFn: async (user: RegisterInput) =>
			await axios.post('http://localhost:4000/v1/users', {
				name: user.name,
				email: user.email,
				password: user.password
			}),
		onSuccess: () => {
			toast.success('Registered successfully!', { duration: 4000 });
		}
	});

	const form = superForm(defaults(zod(registerSchema)), {
		SPA: true,
		validators: zod(registerSchema),
		onUpdate({ form }) {
			if (form.valid) {
				$registerMutation.mutate({
					name: form.data.name,
					email: form.data.email,
					password: form.data.password
				});
			}
		}
	});

	const { form: formData, enhance } = form;
</script>

<form class="min-w-96 p-3" method="POST" use:enhance>
	<div class="text-center">
		<h1 class="text-4xl font-bold">Register</h1>
	</div>
	<Form.Field {form} name="name">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Name</Form.Label>
				<Input {...props} bind:value={$formData.name} />
			{/snippet}
		</Form.Control>
		<Form.FieldErrors />
	</Form.Field>
	<Form.Field {form} name="email">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Email</Form.Label>
				<Input type="email" {...props} bind:value={$formData.email} />
			{/snippet}
		</Form.Control>
		<Form.FieldErrors />
	</Form.Field>
	<Form.Field {form} name="password">
		<Form.Control>
			{#snippet children({ props })}
				<Form.Label>Password</Form.Label>
				<Input type="password" {...props} bind:value={$formData.password} />
			{/snippet}
		</Form.Control>
		<Form.FieldErrors />
	</Form.Field>
	<div class="mt-5 text-center">
		<Form.Button class="w-full">Submit</Form.Button>
		<p class="mt-2">
			Already have account? <a class="text-primary hover:text-secondary" href="/login">Login</a> here
		</p>
	</div>
</form>
