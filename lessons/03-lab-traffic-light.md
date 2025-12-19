---
layout: default
title: 03 - Lab - Budget Traffic Light Visual
---

# 03 - Lab: Budget Traffic Light Visual

In this lab, you'll build a working custom Power BI visual from scratch. By the end, you'll have a visual that:

- Displays KPI cards in a grid
- Color-codes each card based on a "burn rate" (green/yellow/red)
- Shows a category name, numeric value, and mini progress bar
- Works in Power BI's developer mode with real data

**Time estimate:** 45-60 minutes

---

## Demo Data

Before we start, familiarize yourself with the data:

### Option A: Vendor Budget Data

| Category | BurnRate |
|----------|----------|
| Vendor A | 0.45 |
| Vendor B | 0.72 |
| Vendor C | 0.88 |
| Vendor D | 1.05 |
| Vendor E | 1.32 |
| Vendor F | 0.20 |
| Vendor G | 0.95 |
| Vendor H | 1.10 |

**File:** [budget_traffic_light_demo.csv](../examples/data/budget_traffic_light_demo.csv)

### Option B: Task Budget Data

| Task | Budget | Expended | BurnRate |
|------|--------|----------|----------|
| TPS-001 - System Design | 150,000 | 60,000 | 0.40 |
| TPS-002 - Modeling | 200,000 | 165,000 | 0.83 |
| TPS-003 - Testing | 120,000 | 135,000 | 1.12 |
| TPS-004 - Documentation | 80,000 | 30,000 | 0.38 |
| TPS-005 - PM Support | 100,000 | 95,000 | 0.95 |
| TPS-006 - Integration | 180,000 | 210,000 | 1.17 |
| TPS-007 - Training | 90,000 | 72,000 | 0.80 |
| TPS-008 - Sustainment | 160,000 | 155,000 | 0.97 |

**File:** [budget_traffic_light_tasks.csv](../examples/data/budget_traffic_light_tasks.csv)

**Color coding logic:**
- **Green** (< 0.8): On track
- **Yellow** (0.8 - 1.0): Watch
- **Red** (> 1.0): Over budget

---

## Step 1 - Scaffold the project

Open your terminal and navigate to your working folder:

```bash
cd ~/pbi-custom-visuals  # or wherever you created your folder
```

Create a new visual project:

```bash
pbiviz new budgetTrafficLight
cd budgetTrafficLight
```

The `pbiviz new` command creates the following structure:

```
budgetTrafficLight/
├── src/
│   ├── visual.ts           # Main logic (TypeScript)
│   ├── settings.ts         # Format options
│   └── visual.less         # Styles
├── capabilities.json       # Data roles, mappings, metadata
├── pbiviz.json            # Package manifest
├── tsconfig.json          # TypeScript config
├── package.json           # npm dependencies
└── ... other files
```

Install dependencies (if not already done):

```bash
npm install
```

---

## Step 2 - Define data roles in `capabilities.json`

This file tells Power BI what data your visual expects. Open `src/capabilities.json` and replace the default content:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/pbiviz/capabilities/2.6.json",
  "general": {
    "tooltip": true
  },
  "dataRoles": [
    {
      "name": "Category",
      "kind": "Grouping",
      "displayName": "Category",
      "description": "Vendor name, Task ID, or other category label"
    },
    {
      "name": "Value",
      "kind": "Measure",
      "displayName": "Burn Rate",
      "description": "Numeric value (e.g., 0.0 to 2.0 representing expense ratio)"
    }
  ],
  "dataViewMappings": [
    {
      "conditions": [
        {
          "Category": {
            "max": 1
          },
          "Value": {
            "max": 1
          }
        }
      ],
      "categorical": {
        "categories": {
          "for": {
            "in": "Category"
          },
          "dataReductionAlgorithm": {
            "top": {}
          }
        },
        "values": {
          "for": {
            "in": "Value"
          }
        }
      }
    }
  ],
  "objects": {},
  "privileges": []
}
```

**What this does:**
- `dataRoles` - Defines two data inputs:
  - **Category** - A grouping field (like Vendor name)
  - **Value** - A numeric measure (like Burn Rate)
- `dataViewMappings` - Maps Power BI's data structure to your visual
- `privileges` - Empty for now; you could add web access or export permissions later

---

## Step 3 - Implement the visual logic in `visual.ts`

This is the heart of your visual. Open `src/visual.ts` and replace the entire content:

```typescript
import powerbiVisualsApi from "powerbi-visuals-api";
import IVisual = powerbiVisualsApi.extensibility.visual.IVisual;
import VisualConstructorOptions = powerbiVisualsApi.extensibility.visual.VisualConstructorOptions;
import VisualUpdateOptions = powerbiVisualsApi.extensibility.visual.VisualUpdateOptions;
import DataView = powerbiVisualsApi.DataView;

export class Visual implements IVisual {
  private host: powerbiVisualsApi.extensibility.IVisualHost;
  private container: HTMLElement;

  constructor(options: VisualConstructorOptions) {
    this.host = options.host;
    this.container = options.element;
    // Clear any default content
    this.container.innerHTML = "";
  }

  public update(options: VisualUpdateOptions) {
    // Get the data from Power BI
    const dataView: DataView = options.dataViews[0];

    // Clear previous content
    this.container.innerHTML = "";

    // If no data, show a message
    if (!dataView || !dataView.categorical || !dataView.categorical.categories) {
      this.container.innerHTML = "<p>No data available</p>";
      return;
    }

    const categories = dataView.categorical.categories[0];
    const values = dataView.categorical.values[0];

    // Create a grid container
    const gridDiv = document.createElement("div");
    gridDiv.className = "traffic-light-grid";

    // Loop through each data point and create a card
    for (let i = 0; i < categories.values.length; i++) {
      const categoryName = categories.values[i];
      const burnRate = values.values[i] as number;

      // Determine color based on burn rate
      let statusClass: string;
      let statusLabel: string;

      if (burnRate < 0.8) {
        statusClass = "status-green";
        statusLabel = "On Track";
      } else if (burnRate <= 1.0) {
        statusClass = "status-yellow";
        statusLabel = "Watch";
      } else {
        statusClass = "status-red";
        statusLabel = "Over Budget";
      }

      // Create the card HTML
      const card = document.createElement("div");
      card.className = `traffic-light-card ${statusClass}`;

      // Header with category name
      const header = document.createElement("div");
      header.className = "card-header";
      header.textContent = categoryName as string;
      card.appendChild(header);

      // Status label
      const status = document.createElement("div");
      status.className = "card-status";
      status.textContent = statusLabel;
      card.appendChild(status);

      // Numeric value (burn rate)
      const value = document.createElement("div");
      value.className = "card-value";
      value.textContent = burnRate.toFixed(2);
      card.appendChild(value);

      // Mini progress bar (capped at 1.0 for visual purposes)
      const barContainer = document.createElement("div");
      barContainer.className = "card-bar-container";

      const bar = document.createElement("div");
      bar.className = "card-bar";
      const barWidth = Math.min(burnRate * 100, 100); // Cap at 100%
      bar.style.width = `${barWidth}%`;
      barContainer.appendChild(bar);
      card.appendChild(barContainer);

      gridDiv.appendChild(card);
    }

    this.container.appendChild(gridDiv);
  }
}
```

**What this code does:**

1. **Constructor** - Runs once when the visual is created; sets up the HTML container
2. **update()** - Runs every time the data changes; extracts data and renders cards
3. **Color logic** - Burns rates < 0.8 = green, 0.8-1.0 = yellow, > 1.0 = red
4. **Card creation** - For each data point, creates a div with:
   - Category name (header)
   - Status label (On Track/Watch/Over Budget)
   - Numeric value (e.g., "0.45")
   - Mini progress bar showing the ratio

---

## Step 4 - Style the visual with CSS in `visual.less`

Open `style/visual.less` and replace the content:

```less
// Main container
.traffic-light-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
  padding: 20px;
  background: #f5f5f5;
  height: 100%;
  overflow: auto;
}

// Individual card
.traffic-light-card {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border-left: 4px solid #ddd;
  transition: all 0.2s ease;
  cursor: pointer;

  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    transform: translateY(-2px);
  }
}

// Color variants
.traffic-light-card.status-green {
  border-left-color: #2ecc71;
  background: #f0fdf4;
}

.traffic-light-card.status-yellow {
  border-left-color: #f39c12;
  background: #fffbf0;
}

.traffic-light-card.status-red {
  border-left-color: #e74c3c;
  background: #fdf5f5;
}

// Card header (category name)
.card-header {
  font-size: 14px;
  font-weight: 600;
  color: #333;
  margin-bottom: 8px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

// Status label
.card-status {
  font-size: 12px;
  font-weight: 500;
  color: #666;
  margin-bottom: 12px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

// Numeric value
.card-value {
  font-size: 28px;
  font-weight: bold;
  color: #333;
  margin-bottom: 12px;
  font-family: 'Courier New', monospace;
}

// Progress bar
.card-bar-container {
  height: 6px;
  background: #e0e0e0;
  border-radius: 3px;
  overflow: hidden;
}

.card-bar {
  height: 100%;
  background: linear-gradient(90deg, #3498db, #2980b9);
  transition: width 0.3s ease;
}

.traffic-light-card.status-green .card-bar {
  background: linear-gradient(90deg, #2ecc71, #27ae60);
}

.traffic-light-card.status-yellow .card-bar {
  background: linear-gradient(90deg, #f39c12, #e67e22);
}

.traffic-light-card.status-red .card-bar {
  background: linear-gradient(90deg, #e74c3c, #c0392b);
}
```

**Design notes:**
- **Grid layout** - Cards wrap automatically based on container width
- **Color coding** - Green/yellow/red borders match status
- **Progress bar** - Shows the burn rate ratio visually (capped at 100%)
- **Hover effect** - Cards lift slightly when you hover over them
- **Responsive** - Works on different screen sizes

---

## Step 5 - Run the visual in developer mode

In your terminal, still in the `budgetTrafficLight` folder:

```bash
pbiviz start
```

You'll see output like:

```
info    Building visual...
info    Building visual successful
info    Starting local server...
info    Server is running on https://localhost:8080
```

This starts a local server. Keep this terminal running.

---

## Step 6 - Test with Power BI

1. **Open Power BI Desktop**
2. **Create a new blank report**
3. **Load your demo data:**
   - Go to **Get Data** → **CSV** (or **Text/CSV**)
   - Select either `budget_traffic_light_demo.csv` or `budget_traffic_light_tasks.csv`
   - Click **Load**
4. **Add the developer visual:**
   - In the **Visualizations** pane, click the **developer visual** icon (looks like `<>`)
5. **Configure the data:**
   - Drag **Category** (or whatever column name your data has) to the **Category** field
   - Drag **BurnRate** to the **Burn Rate** field
6. **Watch the magic happen!** You should see a grid of colored cards

---

## Step 7 - Troubleshooting the visual

### Cards don't show up

**Check the browser console:**
1. In Power BI, press **F12** to open Developer Tools
2. Go to the **Console** tab
3. Look for red error messages
4. Common issues:
   - Check that your data roles in `capabilities.json` match your column names
   - Verify the column names are spelled correctly in Power BI

**Check the Power BI visual errors:**
- Look at the bottom right of the visual for error messages
- If it says "Waiting for data," you haven't mapped the fields yet

### Colors are wrong

- Double-check the color thresholds in `visual.ts` (< 0.8, <= 1.0, > 1.0)
- Make sure your burn rate values are actually between 0 and 2
- If values are inverted (e.g., 1 means "good"), you may need to change the logic

### Progress bars all look the same

- The bar width is based on `burnRate * 100` with a 100% cap
- If all your values are similar, the bars will look similar (this is correct!)
- Try with data that has more variation (like 0.2 vs 1.5)

---

## Step 8 - Make a change and see live reload

While `pbiviz start` is running, try making a small change:

1. Open `style/visual.less`
2. Change the card `padding` from `16px` to `20px`
3. Save the file
4. Go back to Power BI - the visual should **refresh automatically**

This live reload is super helpful for iterating quickly during development.

---

## What you've accomplished

✅ Scaffolded a custom visual project  
✅ Defined data roles and mappings  
✅ Implemented rendering logic with color coding  
✅ Styled the visual with a responsive grid  
✅ Tested it in Power BI developer mode  
✅ Built a real, working KPI visual!

---

## Next steps (optional, in this session)

If time permits, try:

1. **Change the thresholds** - Edit the numbers (0.8, 1.0) in `visual.ts` and see how colors change
2. **Adjust the colors** - Edit the hex codes in `visual.less` (e.g., `#2ecc71` for green)
3. **Add a title** - Add a `<h1>` to the grid in `visual.ts`
4. **Resize the grid** - Change `minmax(200px, 1fr)` in the CSS to make cards smaller/larger

When you're ready to package this and deploy it as a real custom visual, move to **Lesson 04 - Packaging & Importing**.
