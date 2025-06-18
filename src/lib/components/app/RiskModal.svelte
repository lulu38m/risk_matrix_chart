<script lang="ts">
	import { Badge } from "$lib/components/ui/badge/index.js";
	import { Progress } from "$lib/components/ui/progress/index.js";
	import * as Dialog from "$lib/components/ui/dialog/index.js";

	export let item: {
		actif: string
		color: string
		criticite: number
		vulnerabilite: number
	} | null

	export let isOpen: boolean
	export let onClose: () => void

	const getRiskLevel = (criticite: number, vulnerabilite: number) => {
		const risk = criticite * vulnerabilite
		if (risk <= 25) return { level: "Faible", color: "bg-green-500", textColor: "text-green-700", bgColor: "bg-green-50" }
		if (risk <= 50) return { level: "Modéré", color: "bg-yellow-500", textColor: "text-yellow-700", bgColor: "bg-yellow-50" }
		if (risk <= 75) return { level: "Élevé", color: "bg-orange-500", textColor: "text-orange-700", bgColor: "bg-orange-50" }
		return { level: "Critique", color: "bg-red-500", textColor: "text-red-700", bgColor: "bg-red-50" }
	}

	const getRecommendations = (criticite: number, vulnerabilite: number) => {
		const recommendations = []
		if (criticite >= 8) recommendations.push("Réduire la criticité en implémentant des mesures de redondance")
		if (vulnerabilite >= 8) recommendations.push("Appliquer des correctifs de sécurité prioritaires")
		if (criticite >= 6 && vulnerabilite >= 6) recommendations.push("Surveillance renforcée recommandée")
		if (criticite <= 3 && vulnerabilite <= 3) recommendations.push("Maintenir les mesures de sécurité actuelles")
		return recommendations.length > 0 ? recommendations : ["Évaluer régulièrement les mesures de sécurité"]
	}

	// Variables réactives
	let riskScore = 0
	let riskInfo: any = null
	let recommendations: string[] = []

	$: if (item) {
		riskScore = item.criticite * item.vulnerabilite
		riskInfo = getRiskLevel(item.criticite, item.vulnerabilite)
		recommendations = getRecommendations(item.criticite, item.vulnerabilite)
	}
</script>

{#if item}
	<Dialog.Root open={isOpen} onOpenChange={onClose}>
		<Dialog.Content class="max-w-md">
			<Dialog.Header>
				<Dialog.Title class="flex items-center space-x-3">
					<div class="w-4 h-4 rounded-full" style="background-color: {item.color}" />
					<span>{item.actif}</span>
				</Dialog.Title>
			</Dialog.Header>

			<div class="space-y-6">
				<!-- Niveau de risque -->
				<div class={`p-4 rounded-lg ${riskInfo.bgColor}`}>
					<div class="flex items-center justify-between mb-2">
						<span class="font-medium text-gray-700">Niveau de risque</span>
						<Badge class={`${riskInfo.color} text-white`}>{riskInfo.level}</Badge>
					</div>
					<div class="text-2xl font-bold mb-1">
						<span class={riskInfo.textColor}>{riskScore}/100</span>
					</div>
					<Progress value={riskScore} class="h-2" />
				</div>

				<!-- Métriques détaillées -->
				<div class="grid grid-cols-2 gap-4">
					<!-- Criticité -->
					<div class="space-y-2">
						<div class="flex justify-between items-center">
							<span class="text-sm font-medium text-gray-600">Criticité</span>
							<span class="font-bold text-lg">{item.criticite}/10</span>
						</div>
						<Progress value={item.criticite * 10} class="h-2" />
						<div class="text-xs text-gray-500">
							{item.criticite <= 3
								? "Faible"
								: item.criticite <= 6
									? "Modérée"
									: item.criticite <= 8
										? "Élevée"
										: "Critique"}
						</div>
					</div>

					<!-- Vulnérabilité -->
					<div class="space-y-2">
						<div class="flex justify-between items-center">
							<span class="text-sm font-medium text-gray-600">Vulnérabilité</span>
							<span class="font-bold text-lg">{item.vulnerabilite}/10</span>
						</div>
						<Progress value={item.vulnerabilite * 10} class="h-2" />
						<div class="text-xs text-gray-500">
							{item.vulnerabilite <= 3
								? "Faible"
								: item.vulnerabilite <= 6
									? "Modérée"
									: item.vulnerabilite <= 8
										? "Élevée"
										: "Critique"}
						</div>
					</div>
				</div>

				<!-- Matrice -->
				<div class="space-y-2">
					<h4 class="font-medium text-gray-700">Position sur la matrice</h4>
					<div class="text-sm text-gray-600">
						<p>
							Criticité: {item.criticite} × Vulnérabilité: {item.vulnerabilite} =
							<strong>{riskScore}</strong>
						</p>
					</div>
				</div>

				<!-- Recommandations -->
				<div class="space-y-2">
					<h4 class="font-medium text-gray-700">Recommandations</h4>
					<ul class="space-y-1">
						{#each recommendations as rec}
							<li class="text-sm text-gray-600 flex items-start">
								<span class="text-blue-500 mr-2">•</span>
								{rec}
							</li>
						{/each}
					</ul>
				</div>

				<!-- Actions prioritaires -->
				<div class="space-y-2">
					<h4 class="font-medium text-gray-700">Actions prioritaires</h4>
					<div class="text-sm">
						{#if riskScore >= 75}
							<span class="text-red-600 font-medium">🚨 Action immédiate requise</span>
						{:else if riskScore >= 50}
							<span class="text-orange-600 font-medium">⚠️ Planifier des actions correctives</span>
						{:else if riskScore >= 25}
							<span class="text-yellow-600 font-medium">📋 Surveillance régulière</span>
						{:else}
							<span class="text-green-600 font-medium">✅ Maintenir les mesures actuelles</span>
						{/if}
					</div>
				</div>
			</div>
		</Dialog.Content>
	</Dialog.Root>
{/if}
