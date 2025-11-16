
---

## 7. `lessons/03-lab-traffic-light.md` – Hands-on lab

Create `lessons/03-lab-traffic-light.md`:

```markdown
---
layout: default
title: 03 – Lab: Budget Traffic Light Visual
---

# 03 – Lab: Budget Traffic Light Visual

In this lab, you’ll:

1. Scaffold a visual project
2. Define data roles and mappings
3. Implement the traffic-light logic
4. Style the visual
5. Run it in developer mode with Power BI

---

> 💾 **Download demo data**
>
> The examples below assume you’re using one of these files from this repo:
>
> - [Vendor demo (CSV)](../examples/data/budget_traffic_light_demo.csv)
> - [Task/TPS demo (CSV)](../examples/data/budget_traffic_light_tasks.csv)
>
> You can also open the CSVs in Excel and save them as `.xlsx` if you prefer.


## Step 1 – Scaffold the project

In a working folder (e.g., `pbi-custom-visuals`):

```bash
mkdir pbi-custom-visuals
cd pbi-custom-visuals

pbiviz new budgetTrafficLight
cd budgetTrafficLight

npm install   # if needed
