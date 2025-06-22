<script lang="ts">
	import { onMount } from 'svelte';
	import * as Card from '$lib/components/ui/card/index.js';
	import * as Tabs from '$lib/components/ui/tabs/index.js';
	import RiskForm from '$lib/components/app/RiskForm.svelte';
	import RiskChart from '$lib/components/app/RiskChart.svelte';
	import RiskChartModern from '$lib/components/app/RiskChartModern.svelte';
	import ExcelImport from '$lib/components/app/ExcelImport.svelte';
	import RiskModal from '$lib/components/app/RiskModal.svelte';

	export class RiskItem {
		id: string;
		actif: string;
		criticite: number;
		vulnerabilite: number;
		color: string;

		constructor(id: string, actif: string, criticite: number, vulnerabilite: number, color: string) {
			this.id = id;
			this.actif = actif;
			this.criticite = criticite;
			this.vulnerabilite = vulnerabilite;
			this.color = color;
		}
	}

	let riskItems: RiskItem[] = [];
	let selectedItem: RiskItem | null = null;
	let isModalOpen = false;
	let isModernView = false;

	function openModal(item: RiskItem) {
		selectedItem = item;
		isModalOpen = true;
	}

	function closeModal() {
		selectedItem = null;
		isModalOpen = false;
	}

	function addRiskItem(event: CustomEvent<Omit<RiskItem, 'id' | 'color'>>) {
		const item = event.detail;
		const colors = ['#3b82f6', '#ef4444', '#10b981', '#f59e0b', '#8b5cf6', '#ec4899', '#06b6d4', '#84cc16'];
		const newItem: RiskItem = {
			...item,
			id: Date.now().toString(),
			color: colors[riskItems.length % colors.length]
		};
		riskItems = [...riskItems, newItem];
	}

	function importRiskItems(event: CustomEvent<RiskItem[]>) {
		riskItems = event.detail;
	}

	function removeRiskItem(id: string) {
		riskItems = riskItems.filter((item) => item.id !== id);
	}

	function clearAll() {
		riskItems = [];
	}

	function toggleView() {
		isModernView = !isModernView;
	}
</script>

<div class="min-h-screen bg-gray-50 p-4">
	<div class="max-w-7xl mx-auto">
		<div class="text-center mb-8">
			<h1 class="text-3xl font-bold text-gray-900 mb-2">Cartographie des Risques</h1>
			<p class="text-gray-600">Analysez et visualisez les risques selon leur criticité et vulnérabilité</p>
		</div>

		<div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
			<div class="lg:col-span-1">
				<Card.Card>
					<Card.CardHeader>
						<Card.CardTitle>Gestion des Données</Card.CardTitle>
					</Card.CardHeader>
					<Card.CardContent>
						<Tabs.Root value="form" class="w-full">
							<Tabs.TabsList class="grid w-full grid-cols-2">
								<Tabs.TabsTrigger value="form">Formulaire</Tabs.TabsTrigger>
								<Tabs.TabsTrigger value="import">Import Excel</Tabs.TabsTrigger>
							</Tabs.TabsList>
							<Tabs.TabsContent value="form">
								<RiskForm on:addItem={addRiskItem} />
							</Tabs.TabsContent>
							<Tabs.TabsContent value="import">
								<ExcelImport on:Import={importRiskItems} />
							</Tabs.TabsContent>
						</Tabs.Root>
					</Card.CardContent>
				</Card.Card>

				{#if riskItems.length > 0}
					<Card.Card class="mt-4">
						<Card.CardHeader class="flex flex-row items-center justify-between">
							<Card.CardTitle>Éléments ({riskItems.length})</Card.CardTitle>
							<button on:click={clearAll} class="text-sm text-red-600 hover:text-red-800">
								Tout effacer
							</button>
						</Card.CardHeader>
						<Card.CardContent>
							<div class="space-y-2 max-h-64 overflow-y-auto">
								{#each riskItems as item (item.id)}
									<div class="flex items-center justify-between p-2 bg-gray-50 rounded">
										<div class="flex items-center space-x-2">
											<div class="w-3 h-3 rounded-full" style="background-color: {item.color}" />
											<span class="text-sm font-medium">{item.actif}</span>
										</div>
										<button
											on:click={() => removeRiskItem(item.id)}
											class="text-red-500 hover:text-red-700 text-sm"
										>
											×
										</button>
									</div>
								{/each}
							</div>
						</Card.CardContent>
					</Card.Card>
				{/if}
			</div>

			<div class="lg:col-span-2">
				<Card.Card>
					<Card.CardHeader class="flex flex-row items-center justify-between">
						<Card.CardTitle>Cartographie des Risques</Card.CardTitle>
						<button
							on:click={toggleView}
							class="px-4 py-2 text-sm bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
						>
							{isModernView ? 'Vue Classique' : 'Vue Moderne'}
						</button>
					</Card.CardHeader>
					<Card.CardContent>
						{#if isModernView}
							<RiskChartModern items={riskItems} onPointClick={openModal} />
						{:else}
							<RiskChart items={riskItems} onPointClick={openModal} />
						{/if}
					</Card.CardContent>
				</Card.Card>
			</div>
		</div>

		<RiskModal item={selectedItem} isOpen={isModalOpen} onClose={closeModal} />
	</div>
</div>