
---

## 8. `lessons/04-packaging.md` – Packaging & import

Create `lessons/04-packaging.md`:

```markdown
---
layout: default
title: 04 – Packaging & Importing
---

# 04 – Packaging & Importing

Once your visual works in developer mode, you can package it into a `.pbiviz` file.

---

## Step 1 – Fill out `pbiviz.json`

Open `pbiviz.json` and update a few fields:

- Give the visual a clear name and display name.
- Fill in author details.
- Use a 4-part version number (required):

```json
"visual": {
  "name": "budgetTrafficLight",
  "displayName": "Budget Traffic Light",
  "guid": "budgetTrafficLight12345",
  "visualClassName": "Visual",
  "description": "Simple traffic light visual for budget/burn rates",
  "supportUrl": "https://github.com/YOUR-ORG/pbi-custom-visual-training",
  "gitHubUrl": "https://github.com/YOUR-ORG/pbi-custom-visual-training"
},
"author": {
  "name": "YOUR NAME OR TEAM",
  "email": "you@example.mil"
},
"version": "1.0.0.0"
