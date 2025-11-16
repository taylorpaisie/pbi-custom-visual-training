---
layout: default
title: 04 – Packaging & Importing
---

# 04 – Packaging & Importing

Once your visual works in developer mode, you can package it into a `.pbiviz` file and use it like any other custom visual in Power BI.

This lesson covers:

1. Filling out `pbiviz.json` metadata  
2. Building the `.pbiviz` package with `pbiviz package`  
3. Importing the visual into Power BI Desktop  
4. Wiring it up with the demo data from Lesson 03  
5. Storing the packaged visual for reuse  
6. Ideas for next steps

---

## Step 1 – Fill out `pbiviz.json`

Open `pbiviz.json` in your `budgetTrafficLight` project and make sure the basic visual and author metadata is set.

You should have something like:

```json
{
  "visual": {
    "name": "budgetTrafficLight",
    "displayName": "Budget Traffic Light",
    "guid": "budgetTrafficLight12345",
    "visualClassName": "Visual",
    "description": "Simple KPI card visual for budget/burn rates.",
    "supportUrl": "https://github.com/YOUR-ORG/pbi-custom-visual-training",
    "gitHubUrl": "https://github.com/YOUR-ORG/pbi-custom-visual-training"
  },
  "author": {
    "name": "YOUR NAME OR TEAM",
    "email": "you@example.mil"
  },
  "apiVersion": "5.3.0",
  "version": "1.0.0.0",
  "assets": {
    "icon": "assets/icon.png"
  },
  "style": "style/visual.less",
  "capabilities": "capabilities.json",
  "dependencies": "dependencies.json",
  "stringResources": []
}
