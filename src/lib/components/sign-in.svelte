<script lang="ts">
	import { ConicGradient } from '@skeletonlabs/skeleton';
	import type { ConicStop } from '@skeletonlabs/skeleton';
	import { superForm } from 'sveltekit-superforms/client';
	//import SuperDebug from 'sveltekit-superforms/client/SuperDebug.svelte';
	import { userSchema } from '$lib/config/zod-schemas';
	import { AlertTriangle } from 'lucide-svelte';
	import { i } from "@inlang/sdk-js";
	import { loading } from '$lib/stores';
	export let data;
	const signInSchema = userSchema.pick({ email: true, password: true });
	const { form, errors, enhance, delayed } = superForm(data.form, {
		taintedMessage: null,
		validators: signInSchema,
		delayMs: 0
	});
	const conicStops: ConicStop[] = [
		{ color: 'transparent', start: 0, end: 25 },
		{ color: 'rgb(var(--color-primary-900))', start: 75, end: 100 }
	];

	$: loading.set($delayed)
</script>

<form method="POST" action="/auth/sign-in" use:enhance>
	<!--<SuperDebug data={$form} />-->
	{#if $errors._errors}
		<aside class="alert variant-filled-error mt-6">
			<!-- Icon -->
			<div><AlertTriangle size="42" /></div>
			<!-- Message -->
			<div class="alert-message">
				<h3 class="h3">{i("signinProblem")}</h3>
				<p>{$errors._errors}</p>
			</div>
		</aside>
	{/if}
	<div class="mt-6">
		<label class="label">
			<span class="sr-only">{i("email")}</span>
			<input
				id="email"
				name="email"
				type="email"
				placeholder="{i("email")}"
				autocomplete="email"
				data-invalid={$errors.email}
				bind:value={$form.email}
				class="input"
				class:input-error={$errors.email}
			/>
			{#if $errors.email}
				<small>{$errors.email}</small>
			{/if}
		</label>
	</div>

	<div class="mt-6">
		<label class="label">
			<span class="sr-only">{i("password")}</span>
			<input
				id="password"
				name="password"
				type="password"
				placeholder="{i("password")}"
				data-invalid={$errors.password}
				bind:value={$form.password}
				class="input"
				class:input-error={$errors.password}
			/>
			{#if $errors.password}
				<small>{$errors.password}</small>
			{/if}
		</label>
	</div>

	<div class="mt-6">
		<button type="submit" class="btn variant-filled-primary w-full" disabled={$delayed}>
			{#if $delayed}<ConicGradient stops={conicStops} spin width="w-6" />{:else}{i("signin")}{/if}
		</button>
	</div>
	<div class="flex flex-row justify-center items-center mt-10">
		<a href="/auth/password/reset" class="font-semibold">{i("forgotPassword")}</a>
	</div>
</form>
