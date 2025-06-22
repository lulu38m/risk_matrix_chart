<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	import { Button } from "$lib/components/ui/button/index.js";
	import { Input } from "$lib/components/ui/input/index.js";
	import { Label } from "$lib/components/ui/label/index.js";
	import { Slider } from "$lib/components/ui/slider/index.js";



	let actif = '';
	let criticite = [5];
	let vulnerabilite = [5];

	const dispatch = createEventDispatcher();

	const handleSubmit = (e: Event) => {
		e.preventDefault();
		if (actif.trim()) {
			dispatch('addItem', {
				actif: actif.trim(),
				criticite: criticite[0],
				vulnerabilite: vulnerabilite[0],
			});

			actif = '';
			criticite = [5];
			vulnerabilite = [5];
		}
	};
</script>

<form on:submit|preventDefault={handleSubmit} class="space-y-4">
	<div>
		<Label for="actif" class="pb-2">Actif</Label>
		<Input
			id="actif"
			bind:value={actif}
			placeholder="Nom de l'actif..."
			required
		/>
	</div>

	<div>
		<Label>Criticité: {criticite[0]}</Label>
		<Slider type="multiple" bind:value={criticite} max={10} min={1} step={1} class="mt-2" />
		<div class="flex justify-between text-xs text-gray-500 mt-1">
			<span>Faible (1)</span>
			<span>Élevée (10)</span>
		</div>
	</div>

	<div>
		<Label>Vulnérabilité: {vulnerabilite[0]}</Label>
		<Slider type="multiple" bind:value={vulnerabilite} max={10} min={1} step={1} class="mt-2" />
		<div class="flex justify-between text-xs text-gray-500 mt-1">
			<span>Faible (1)</span>
			<span>Élevée (10)</span>
		</div>
	</div>

	<Button type="submit" class="w-full">
		Ajouter l'élément
	</Button>
</form>
