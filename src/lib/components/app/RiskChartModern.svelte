<script lang="ts">
	import type { RiskItem } from '../../../routes/+page.svelte';

	export let items: RiskItem[] = [];
	export let onPointClick: (item: RiskItem) => void;

	const chartWidth = 600;
	const chartHeight = 400;
	const padding = 80;

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
		<div class="relative bg-white rounded-lg p-6">
			<svg width={chartWidth} height={chartHeight} class="w-full h-full">
				<!-- Fond blanc -->
				<rect width="100%" height="100%" fill="white" />

				<!-- Axes principaux -->
				<line
					x1={chartWidth / 2}
					y1={padding}
					x2={chartWidth / 2}
					y2={chartHeight - padding}
					stroke="#374151"
					stroke-width="2"
				/>
				<line
					x1={padding}
					y1={chartHeight / 2}
					x2={chartWidth - padding}
					y2={chartHeight / 2}
					stroke="#374151"
					stroke-width="2"
				/>

				<!-- Flèches des axes -->
				<defs>
					<marker id="arrowhead" markerWidth="10" markerHeight="7"
									refX="9" refY="3.5" orient="auto">
						<polygon points="0 0, 10 3.5, 0 7" fill="#374151" />
					</marker>
				</defs>

				<!-- Axe vertical avec flèche -->
				<line
					x1={chartWidth / 2}
					y1={padding}
					x2={chartWidth / 2}
					y2={padding - 20}
					stroke="#374151"
					stroke-width="2"
					marker-end="url(#arrowhead)"
				/>

				<!-- Axe horizontal avec flèche -->
				<line
					x1={chartWidth - padding}
					y1={chartHeight / 2}
					x2={chartWidth - padding + 20}
					y2={chartHeight / 2}
					stroke="#374151"
					stroke-width="2"
					marker-end="url(#arrowhead)"
				/>

				<!-- Labels des axes -->
				<text
					x={chartWidth / 2}
					y={padding - 35}
					text-anchor="middle"
					class="text-sm font-bold fill-gray-700 uppercase tracking-wide"
				>
					VULNÉRABILITÉ
				</text>
				<text
					x={chartWidth - padding + 35}
					y={chartHeight / 2 + 5}
					text-anchor="middle"
					class="text-sm font-bold fill-gray-700 uppercase tracking-wide"
				>
					CRITICITÉ
				</text>

				<!-- Labels des quadrants -->
				<text
					x={padding + (chartWidth - 2 * padding) / 4}
					y={chartHeight - padding + 25}
					text-anchor="middle"
					class="text-xs fill-gray-500"
				>
					Faible --
				</text>
				<text
					x={chartWidth - padding - (chartWidth - 2 * padding) / 4}
					y={chartHeight - padding + 25}
					text-anchor="middle"
					class="text-xs fill-gray-500"
				>
					Soutien ++
				</text>

				<!-- Graduations discrètes sur les axes -->
				{#each [2, 4, 6, 8, 10] as val}
					<!-- Graduations axe X (criticité) -->
					<line
						x1={padding + ((val - 1) / 9) * (chartWidth - 2 * padding)}
						y1={chartHeight / 2 - 5}
						x2={padding + ((val - 1) / 9) * (chartWidth - 2 * padding)}
						y2={chartHeight / 2 + 5}
						stroke="#9ca3af"
						stroke-width="1"
					/>
					<!-- Graduations axe Y (vulnérabilité) -->
					<line
						x1={chartWidth / 2 - 5}
						y1={chartHeight - padding - ((val - 1) / 9) * (chartHeight - 2 * padding)}
						x2={chartWidth / 2 + 5}
						y2={chartHeight - padding - ((val - 1) / 9) * (chartHeight - 2 * padding)}
						stroke="#9ca3af"
						stroke-width="1"
					/>
				{/each}

				<!-- Points -->
				{#each items as item, index}
					{@const { x, y } = getPosition(item.criticite, item.vulnerabilite)}
					{@const riskInfo = getRiskLevel(item.criticite, item.vulnerabilite)}
					<g class="cursor-pointer" on:click={() => onPointClick(item)}>
						<!-- Ombre du cercle -->
						<circle
							cx={x + 2}
							cy={y + 2}
							r="18"
							fill="rgba(0,0,0,0.1)"
						/>
						<!-- Cercle principal -->
						<circle
							cx={x}
							cy={y}
							r="18"
							fill={item.color}
							stroke="white"
							stroke-width="3"
							class="hover:r-20 transition-all duration-200"
						/>
						<!-- Numéro -->
						<text
							x={x}
							y={y + 6}
							text-anchor="middle"
							class="text-sm font-bold fill-white pointer-events-none"
						>
							{index + 1}
						</text>
					</g>
				{/each}
			</svg>

			<!-- Légende moderne -->
			<div class="mt-6 flex flex-wrap justify-center gap-4 text-xs">
				<div class="flex items-center space-x-2 bg-green-50 px-3 py-2 rounded-full">
					<div class="w-3 h-3 bg-green-500 rounded-full"></div>
					<span class="font-medium text-green-700">Risque Faible (1-25)</span>
				</div>
				<div class="flex items-center space-x-2 bg-yellow-50 px-3 py-2 rounded-full">
					<div class="w-3 h-3 bg-yellow-500 rounded-full"></div>
					<span class="font-medium text-yellow-700">Risque Modéré (26-50)</span>
				</div>
				<div class="flex items-center space-x-2 bg-red-50 px-3 py-2 rounded-full">
					<div class="w-3 h-3 bg-red-500 rounded-full"></div>
					<span class="font-medium text-red-700">Risque Élevé (51-75)</span>
				</div>
				<div class="flex items-center space-x-2 bg-red-100 px-3 py-2 rounded-full">
					<div class="w-3 h-3 bg-red-600 rounded-full"></div>
					<span class="font-medium text-red-800">Risque Critique (76-100)</span>
				</div>
			</div>
		</div>
	{/if}
</div>