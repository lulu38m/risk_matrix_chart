<script lang="ts">

	import type { RiskItem } from '../../../routes/+page.svelte';

	export let items: RiskItem[] = [];
	export let onPointClick: (item: RiskItem) => void;

	const chartWidth = 600;
	const chartHeight = 400;
	const padding = 60;

	const getPosition = (criticite: number, vulnerabilite: number) => {
		const x = padding + ((criticite - 1) / 9) * (chartWidth - 2 * padding);
		const y = chartHeight - padding - ((vulnerabilite - 1) / 9) * (chartHeight - 2 * padding);
		return { x, y };
	};

	const getRiskLevel = (criticite: number, vulnerabilite: number) => {
		const risk = criticite * vulnerabilite;
		if (risk <= 25) return { level: 'Faible', color: '#10b981' };
		if (risk <= 50) return { level: 'Modéré', color: '#f59e0b' };
		if (risk <= 75) return { level: 'Élevé', color: '#ef4444' };
		return { level: 'Critique', color: '#dc2626' };
	};
</script>

<div class="w-full">
	{#if items.length === 0}
		<div class="flex items-center justify-center h-96 text-gray-500">
			<div class="text-center">
				<p class="text-lg mb-2">Aucun élément à afficher</p>
				<p class="text-sm">Ajoutez des éléments via le formulaire ou importez un fichier Excel</p>
			</div>
		</div>
	{:else}
		<div class="relative">
			<svg width={chartWidth} height={chartHeight} class="border rounded-lg bg-white">
				<defs>
					<pattern id="grid" width="60" height="40" patternUnits="userSpaceOnUse">
						<path d="M 60 0 L 0 0 0 40" fill="none" stroke="#f3f4f6" stroke-width="1" />
					</pattern>
				</defs>
				<rect width="100%" height="100%" fill="url(#grid)" />

				<!-- Zones de risque -->
				{#each Array(3) as _, rowIndex}
					{#each Array(3) as _, colIndex}
						{@const criticite = colIndex * 3 + 2}
						{@const vulnerabilite = (2 - rowIndex) * 3 + 2}
						<rect
							x={padding + (colIndex * (chartWidth - 2 * padding)) / 3}
							y={padding + (rowIndex * (chartHeight - 2 * padding)) / 3}
							width={(chartWidth - 2 * padding) / 3}
							height={(chartHeight - 2 * padding) / 3}
							fill={getRiskLevel(criticite, vulnerabilite).color}
							opacity="0.1"
						/>
					{/each}
				{/each}

				<!-- Axes -->
				<line
					x1={padding}
					y1={chartHeight - padding}
					x2={chartWidth - padding}
					y2={chartHeight - padding}
					stroke="#374151"
					stroke-width="2"
				/>
				<line
					x1={padding}
					y1={padding}
					x2={padding}
					y2={chartHeight - padding}
					stroke="#374151"
					stroke-width="2"
				/>

				<!-- Labels -->
				<text
					x={chartWidth / 2}
					y={chartHeight - 20}
					text-anchor="middle"
					class="text-sm font-medium fill-gray-700"
				>
					CRITICITÉ
				</text>
				<text
					x={20}
					y={chartHeight / 2}
					text-anchor="middle"
					transform={`rotate(-90 20 ${chartHeight / 2})`}
					class="text-sm font-medium fill-gray-700"
				>
					VULNÉRABILITÉ
				</text>

				<!-- Graduations -->
				{#each Array(10) as _, i}
					<g>
						<text
							x={padding + (i / 9) * (chartWidth - 2 * padding)}
							y={chartHeight - padding + 15}
							text-anchor="middle"
							class="text-xs fill-gray-500"
						>
							{i + 1}
						</text>
						<text
							x={padding - 15}
							y={chartHeight - padding - (i / 9) * (chartHeight - 2 * padding) + 4}
							text-anchor="middle"
							class="text-xs fill-gray-500"
						>
							{i + 1}
						</text>
					</g>
				{/each}

				<!-- Points -->
				{#each items as item, index}
					{@const { x, y } = getPosition(item.criticite, item.vulnerabilite)}
					<g>
						<circle
							cx={x}
							cy={y}
							r="12"
							fill={item.color}
							stroke="white"
							stroke-width="2"
							class="cursor-pointer hover:r-14 transition-all"
							on:click={() => onPointClick(item)}
						/>
						<text
							x={x}
							y={y + 4}
							text-anchor="middle"
							class="text-xs font-bold fill-white pointer-events-none"
						>
							{index + 1}
						</text>
					</g>
				{/each}
			</svg>

			<!-- Légende -->
			<div class="mt-4 grid grid-cols-2 md:grid-cols-4 gap-2 text-xs">
				<div class="flex items-center space-x-2">
					<div class="w-4 h-4 bg-green-500 opacity-20 rounded"></div>
					<span>Risque Faible</span>
				</div>
				<div class="flex items-center space-x-2">
					<div class="w-4 h-4 bg-yellow-500 opacity-20 rounded"></div>
					<span>Risque Modéré</span>
				</div>
				<div class="flex items-center space-x-2">
					<div class="w-4 h-4 bg-red-500 opacity-20 rounded"></div>
					<span>Risque Élevé</span>
				</div>
				<div class="flex items-center space-x-2">
					<div class="w-4 h-4 bg-red-600 opacity-20 rounded"></div>
					<span>Risque Critique</span>
				</div>
			</div>
		</div>
	{/if}
</div>
