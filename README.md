# 🎯 Cartographie des Risques

Une application web moderne pour analyser et visualiser les risques selon leur criticité et vulnérabilité. Créée avec SvelteKit, TypeScript, Tailwind CSS et Shadcn.


## ✨ Fonctionnalités

### 📊 Visualisation Interactive
- **Graphique scatter plot** avec zones de risque colorées
- **Points cliquables** avec modal détaillée
- **Axes personnalisables** (Criticité × Vulnérabilité)
- **Légende** des niveaux de risque

### 📝 Saisie de Données
- **Formulaire intuitif** avec sliders pour criticité/vulnérabilité
- **Import Excel/CSV** avec validation automatique
- **Téléchargement de modèle** pour faciliter l'import
- **Gestion des éléments** (ajout, suppression, liste)

### 🔍 Analyse Détaillée
- **Modal interactive** au clic sur les points
- **Score de risque calculé** (criticité × vulnérabilité)
- **Recommandations personnalisées** selon le niveau
- **Actions prioritaires** avec indicateurs visuels

### 🎨 Interface Moderne
- **Design responsive** adapté mobile/desktop
- **Thème cohérent** avec shadcn/ui
- **Animations fluides** et interactions intuitives
- **Accessibilité** optimisée

## 🚀 Installation

### Prérequis
- Node.js 18+ 
- npm ou yarn
- SvelteKit

### Installation manuelle

```bash
# Cloner le projet
git clone https://github.com/lulu38m?tab=repositories
cd risk-mapping

# Installer les dépendances
npm install

# Lancer en développement
npm run dev
```

L'application sera disponible sur [http://localhost:3000](http://localhost:3000)

## 📋 Utilisation

### 1. Ajouter des Actifs

**Via le formulaire :**
1. Saisissez le nom de l'actif
2. Ajustez la criticité (1-10) avec le slider
3. Ajustez la vulnérabilité (1-10) avec le slider
4. Cliquez sur "Ajouter l'élément"

**Via import Excel/CSV :**
1. Préparez un fichier avec les colonnes : `Actif`, `Criticité`, `Vulnérabilité`
2. Ou téléchargez le modèle fourni
3. Importez le fichier via l'onglet "Import Excel"

### 2. Analyser les Risques

- **Visualisation** : Les points sont positionnés selon leur criticité (X) et vulnérabilité (Y)
- **Zones colorées** : 
  - 🟢 Vert = Risque faible (≤25)
  - 🟡 Jaune = Risque modéré (26-50)
  - 🟠 Orange = Risque élevé (51-75)
  - 🔴 Rouge = Risque critique (>75)

### 3. Consulter les Détails

Cliquez sur n'importe quel point pour ouvrir la modal avec :
- Score de risque détaillé
- Métriques visuelles (barres de progression)
- Recommandations personnalisées
- Actions prioritaires

## 📁 Structure du Projet

```
├── route/
│   ├── +page.svlete             # Page principale
├── components/
│   ├── RiskChart.svlete         # Graphique principal
│   ├── RiskModal.svlete       # Modal de détails
│   ├── RiskForm.svlete       # Formulaire de saisie 
│   ├── ExcelImporte.svelte      # Import de fichiers
│   └── ui/                   # Composants shadcn/ui
├── lib/
│   └── utils.ts              # Utilitaires
└── README.md
```

## 🎨 Technologies Utilisées

- **Framework** : SvelteKit
- **Language** : TypeScript
- **Styling** : Tailwind CSS
- **UI Components** : shadcn/ui
- **Icons** : Lucide Svelte
  
## 📊 Format des Données

### Structure CSV/Excel attendue :

| Actif | Criticité | Vulnérabilité |
|-------|-----------|---------------|
| Serveur Web | 8 | 6 |
| Base de données | 9 | 7 |
| Firewall | 5 | 3 |
| Poste utilisateur | 4 | 8 |

### Règles de validation :
- **Actif** : Texte libre (obligatoire)
- **Criticité** : Nombre entre 1 et 10
- **Vulnérabilité** : Nombre entre 1 et 10

## 🔧 Personnalisation

### Modifier les seuils de risque

Dans `components/RiskChart.svlete` et `components/RiskModale.svelte` :

```typescript
const getRiskLevel = (criticite: number, vulnerabilite: number) => {
  const risk = criticite * vulnerabilite
  if (risk <= 25) return { level: "Faible", color: "#10b981" }
  if (risk <= 50) return { level: "Modéré", color: "#f59e0b" }
  if (risk <= 75) return { level: "Élevé", color: "#ef4444" }
  return { level: "Critique", color: "#dc2626" }
}
```

### Ajouter de nouvelles couleurs

Dans `route/+page.svelte` :

```typescript
const colors = [
  "#3b82f6", "#ef4444", "#10b981", "#f59e0b", 
  "#8b5cf6", "#ec4899", "#06b6d4", "#84cc16"
]
```

## 🤝 Contribution

1. Fork le projet
2. Créez une branche feature (`git checkout -b feature/nouvelle-fonctionnalite`)
3. Committez vos changements (`git commit -m 'Ajout nouvelle fonctionnalité'`)
4. Push vers la branche (`git push origin feature/nouvelle-fonctionnalite`)
5. Ouvrez une Pull Request

## 📝 Roadmap

- [ ] Export PDF de la cartographie
- [ ] Filtres avancés par niveau de risque
- [ ] Historique des modifications
- [ ] Commentaires sur les actifs
- [ ] Alertes automatiques
- [ ] API REST pour intégration
- [ ] Authentification utilisateur
- [ ] Tableaux de bord personnalisés

## 📄 Licence

Ce projet est sous licence MIT. Voir le fichier `LICENSE` pour plus de détails.

## 🆘 Support

Pour toute question ou problème :
- Ouvrez une [issue]([https://github.com/votre-repo/issues](https://github.com/lulu38m/risk_matrix_chart/issues))
- Consultez la [documentation]([https://github.com/votre-repo/wiki](https://github.com/lulu38m/risk_matrix_chart))
- Contactez l'équipe de développement

---

**Développé avec ❤️ par lulu**

*Dernière mise à jour : Juin 2025*
