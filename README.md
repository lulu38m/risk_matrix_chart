# 🎯 Risk Mapping

A modern web application to analyze and visualize risks based on their criticality and vulnerability. Built with SvelteKit, TypeScript, Tailwind CSS, and Shadcn.

## ✨ Features

### 📊 Interactive Visualization
- **Scatter plot chart** with colored risk zones
- **Clickable points** with detailed modal
- **Customizable axes** (Criticality × Vulnerability)
- **Legend** for risk levels

### 📝 Data Entry
- **Intuitive form** with sliders for criticality/vulnerability
- **Excel/CSV import** with automatic validation
- **Template download** to facilitate import
- **Asset management** (add, delete, list)

### 🔍 Detailed Analysis
- **Interactive modal** on point click
- **Calculated risk score** (criticality × vulnerability)
- **Personalized recommendations** based on level
- **Priority actions** with visual indicators

### 🎨 Modern Interface
- **Responsive design** adapted for mobile/desktop
- **Consistent theme** with shadcn/ui
- **Smooth animations** and intuitive interactions
- **Optimized accessibility**

## 🚀 Installation

### Prerequisites
- Node.js 18+ 
- npm or yarn
- SvelteKit

### Manual Installation

```bash
# Clone the project
git clone https://github.com/lulu38m/risk_matrix_chart

# Install dependencies
npm install

# Run in development
npm run dev
```

The application will be available at [http://localhost:3000](http://localhost:3000)

## 📋 Usage

### 1. Adding Assets

**Via the form:**
1. Enter the asset name
2. Adjust criticality (1-10) with the slider
3. Adjust vulnerability (1-10) with the slider
4. Click "Add Asset"

**Via Excel/CSV import:**
1. Prepare a file with columns: `Asset`, `Criticality`, `Vulnerability`
2. Or download the provided template
3. Import the file via the "Excel Import" tab

### 2. Risk Analysis

- **Visualization**: Points are positioned according to their criticality (X) and vulnerability (Y)
- **Colored zones**: 
  - 🟢 Green = Low risk (≤25)
  - 🟡 Yellow = Moderate risk (26-50)
  - 🟠 Orange = High risk (51-75)
  - 🔴 Red = Critical risk (>75)

### 3. View Details

Click on any point to open the modal with:
- Detailed risk score
- Visual metrics (progress bars)
- Personalized recommendations
- Priority actions

## 📁 Project Structure

```
├── route/
│   ├── +page.svelte             # Main page
├── components/
│   ├── RiskChart.svelte         # Main chart
│   ├── RiskModal.svelte         # Details modal
│   ├── RiskForm.svelte          # Input form
│   ├── ExcelImporter.svelte     # File import
│   └── ui/                      # shadcn/ui components
├── lib/
│   └── utils.ts                 # Utilities
└── README.md
```

## 🎨 Technologies Used

- **Framework**: SvelteKit
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **UI Components**: shadcn/ui
- **Icons**: Lucide Svelte
  
## 📊 Data Format

### Expected CSV/Excel structure:

| Asset | Criticality | Vulnerability |
|-------|-------------|---------------|
| Web Server | 8 | 6 |
| Database | 9 | 7 |
| Firewall | 5 | 3 |
| User Workstation | 4 | 8 |

### Validation rules:
- **Asset**: Free text (required)
- **Criticality**: Number between 1 and 10
- **Vulnerability**: Number between 1 and 10

## 🔧 Customization

### Modify risk thresholds

In `components/RiskChart.svelte` and `components/RiskModal.svelte`:

```typescript
const getRiskLevel = (criticality: number, vulnerability: number) => {
  const risk = criticality * vulnerability
  if (risk <= 25) return { level: "Low", color: "#10b981" }
  if (risk <= 50) return { level: "Moderate", color: "#f59e0b" }
  if (risk <= 75) return { level: "High", color: "#ef4444" }
  return { level: "Critical", color: "#dc2626" }
}
```

### Add new colors

In `route/+page.svelte`:

```typescript
const colors = [
  "#3b82f6", "#ef4444", "#10b981", "#f59e0b", 
  "#8b5cf6", "#ec4899", "#06b6d4", "#84cc16"
]
```

## 🤝 Contributing

1. Fork the project
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Commit your changes (`git commit -m 'Add new feature'`)
4. Push to the branch (`git push origin feature/new-feature`)
5. Open a Pull Request

## 📝 Roadmap

- [ ] PDF export of the risk map
- [ ] Advanced filters by risk level
- [ ] Change history
- [ ] Asset comments
- [ ] Automatic alerts
- [ ] REST API for integration
- [ ] User authentication
- [ ] Custom dashboards

## 📄 License

This project is licensed under the MIT License. See the `LICENSE` file for more details.

## 🆘 Support

For any questions or issues:
- Open an [issue](https://github.com/lulu38m/risk_matrix_chart/issues)
- Check the [documentation](https://github.com/lulu38m/risk_matrix_chart)
- Contact the development team

---

**Developed with ❤️ by lulu**

*Last updated: June 2025*
