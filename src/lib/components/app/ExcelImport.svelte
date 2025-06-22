<script lang="ts">
	import { createEventDispatcher } from "svelte"
	import { onMount } from "svelte"
	import { Button } from "$lib/components/ui/button/index.js";


	let file: File | null = null
	let error = ""
	let isLoading = false

	const dispatch = createEventDispatcher()

	interface RiskItem {
		id: string
		actif: string
		criticite: number
		vulnerabilite: number
		color: string
	}

	const handleFileChange = (e: Event) => {
		const target = e.target as HTMLInputElement
		const selectedFile = target.files?.[0]
		if (selectedFile) {
			file = selectedFile
			error = ""
			console.log("Fichier sélectionné:", selectedFile.name)
		}
	}

	const parseCSV = (text: string): RiskItem[] => {
		const lines = text.split("\n").filter((line) => line.trim())
		const headers = lines[0].split(",").map((h) => h.trim().toLowerCase())

		const actifIndex = headers.findIndex(h => h.includes("actif") || h.includes("asset"))
		const criticiteIndex = headers.findIndex(h => h.includes("criticité") || h.includes("criticite") || h.includes("criticality"))
		const vulnerabiliteIndex = headers.findIndex(h => h.includes("vulnérabilité") || h.includes("vulnerabilite") || h.includes("vulnerability"))

		if (actifIndex === -1 || criticiteIndex === -1 || vulnerabiliteIndex === -1) {
			throw new Error("Colonnes requises non trouvées. Assurez-vous d'avoir les colonnes: Actif, Criticité, Vulnérabilité")
		}

		const colors = ["#3b82f6", "#ef4444", "#10b981", "#f59e0b", "#8b5cf6", "#ec4899", "#06b6d4", "#84cc16"]

		return lines.slice(1).map((line, index) => {
			const values = line.split(",").map(v => v.trim())
			return {
				id: Date.now().toString() + index,
				actif: values[actifIndex] || `Actif ${index + 1}`,
				criticite: Math.max(1, Math.min(10, Number.parseInt(values[criticiteIndex]) || 5)),
				vulnerabilite: Math.max(1, Math.min(10, Number.parseInt(values[vulnerabiliteIndex]) || 5)),
				color: colors[index % colors.length]
			}
		})
	}

	const handleImport = async () => {
		if (!file) return
		isLoading = true
		error = ""

		try {
			const text = await file.text()
			const items = parseCSV(text)
			dispatch("import", { items })
			file = null
			const fileInput = document.getElementById("file-input") as HTMLInputElement
			if (fileInput) fileInput.value = ""
		} catch (err) {
			error = err instanceof Error ? err.message : "Erreur lors de l'import du fichier"
		} finally {
			isLoading = false
		}
	}

	const downloadTemplate = () => {
		const csvContent = "Actif,Criticité,Vulnérabilité\nServeur Web,8,6\nBase de données,9,7\nFirewall,5,3\nPoste utilisateur,4,8"
		const blob = new Blob([csvContent], { type: "text/csv" })
		const url = window.URL.createObjectURL(blob)
		const a = document.createElement("a")
		a.href = url
		a.download = "template_risques.csv"
		a.click()
		window.URL.revokeObjectURL(url)
	}
</script>

<div class="space-y-4">
	<div>
		<label for="file-input">Fichier CSV/Excel</label>
		<input id="file-input" type="file" accept=".csv,.xlsx,.xls" onchange={handleFileChange} class="mt-1" />
		<p class="text-xs text-gray-500 mt-1">Formats supportés: CSV, Excel (.xlsx, .xls)</p>
	</div>

	{#if error}
		<div class="bg-red-100 text-red-800 px-4 py-2 rounded text-sm">
			{error}
		</div>
	{/if}

	<div class="flex space-x-2">
		<button onclick={handleImport} disabled={!file || isLoading} class="flex-1 bg-blue-500 text-white py-2 px-4 rounded disabled:opacity-50 hover:bg-blue-600 disabled:cursor-not-allowed">
			{isLoading ? "Import en cours..." : "Importer"}
		</button>
		<button onclick={downloadTemplate} class="flex-1 border border-gray-300 py-2 px-4 rounded cursor-pointer hover:bg-gray-50">
			Télécharger modèle
		</button>
	</div>

	<div class="text-xs text-gray-600 space-y-1">
		<p><strong>Format attendu:</strong></p>
		<p>• Colonne 1: Actif (nom de l'élément)</p>
		<p>• Colonne 2: Criticité (1-10)</p>
		<p>• Colonne 3: Vulnérabilité (1-10)</p>
	</div>
</div>