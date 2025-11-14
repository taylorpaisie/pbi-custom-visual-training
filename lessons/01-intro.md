---
layout: default
title: 01 – Why Custom Visuals?
---

# 01 – Why Custom Visuals?

## When built-in visuals aren't enough

Power BI has a lot of built-in visuals and AppSource options. They’re great until you need:

- A very **specific layout** or KPI look (e.g., internal “Navy-style” dashboards).
- A **custom interaction** or animation that doesn’t exist yet.
- A visual that **encodes your team’s logic** directly (e.g., special color rules, categories, thresholds).

In those cases, we can build our own visual using the **Power BI Visuals SDK** and the `pbiviz` command-line tool.

---

## What you’ll build today

In this workshop, we’ll build a tiny but complete visual:

> **Budget Traffic Light**  
> - One category (e.g., Vendor or Task)  
> - One value (e.g., % of budget used or burn rate)  
> - A colored circle for each row:  
>   - **Green** if value < 0.8  
>   - **Yellow** if 0.8–1.0  
>   - **Red** if > 1.0  

It’s deliberately simple so you can focus on the full pipeline:

1. Scaffold a project with `pbiviz new`
2. Define data roles in `capabilities.json`
3. Render HTML in `visual.ts`
4. Style with `visual.less`
5. Package to `.pbiviz` and import into Power BI

Once you understand that flow, you can swap the simple traffic light for React, D3, or whatever else your heart (and security policies) desire.
