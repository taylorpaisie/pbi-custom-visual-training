---
layout: default
title: 01 – Overview & Goals
---

# 01 – Overview & Goals

This mini-course walks through building a **custom Power BI visual** with `pbiviz`, using a realistic example:

> A **Budget Traffic Light** visual that shows KPI cards for vendors/tasks with  
> - Color-coded status (green / yellow / red)  
> - A numeric value (e.g., burn rate)  
> - A mini bar to show relative magnitude  

By the end, you’ll be able to:

- Scaffold a custom visual project using `pbiviz new`
- Define data roles and mappings using `capabilities.json`
- Render your own layout using HTML + CSS + TypeScript
- Package the visual as a `.pbiviz` file
- Import and use it in regular Power BI reports

---

## Who this is for

This training is aimed at:

- **Power BI users / analysts** who want visuals beyond the built-in gallery
- **Developers / tinkerers** who are comfortable editing code (TypeScript/JS) and using the command line

You **don’t** need to be a hardcore front-end engineer, but you should be okay with:

- Running `npm` and `pbiviz` commands
- Copy-pasting small TypeScript and CSS snippets
- Using GitHub / Git if you want to clone this repo

---

## What we’re building

We’re building a custom visual called **Budget Traffic Light**:

- Data roles:
  - **Category** – Vendor name, Task ID, TPS description, etc.
  - **Value** – A numeric ratio (e.g., `Expended / Budget` burn rate)
- Behavior:
  - `< 0.8` → Green (“On track”)
  - `0.8 – 1.0` → Yellow (“Watch”)
  - `> 1.0` → Red (“Over budget”)

The visual renders a **grid of KPI cards** rather than a standard chart, to showcase why custom visuals are worth the effort.

---

## Course structure

The lessons are designed to be followed in order:

1. **01 – Overview & Goals** (this page)  
   - Big-picture view, prerequisites, and structure

2. **02 – Environment Setup**  
   - Install `pbiviz` and Node.js  
   - Enable developer mode in Power BI Desktop  
   - Create a new visual project

3. **03 – Lab: Budget Traffic Light Visual**  
   - Define data roles in `capabilities.json`  
   - Implement a card-based visual in `visual.ts`  
   - Add styles with `visual.less`  
   - Run it in developer mode with sample data

4. **04 – Packaging & Importing**  
   - Fill out `pbiviz.json` metadata  
   - Build a `.pbiviz` package  
   - Import and use the visual like any other custom visual

You can teach this as:

- A **single 90–120 minute workshop**, or  
- A **two-part series** (setup + dev, then packaging + usage)

---

## Repo structure (high level)

This repo is structured like this:

```text
pbi-custom-visual-training/
  lessons/
    01-intro.md
    02-setup.md
    03-lab-traffic-light.md
    04-packaging.md
  examples/
    data/
      budget_traffic_light_demo.csv
      budget_traffic_light_tasks.csv
    budgetTrafficLight-final/
      dist/
        budgetTrafficLight.pbiviz   # packaged visual (optional)
      README.md                     # notes / changelog
