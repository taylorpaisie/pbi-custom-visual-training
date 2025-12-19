---
layout: default
title: 04 - Packaging & Importing
---

# 04 - Packaging & Importing

Once your visual works in developer mode, you can **package it** into a `.pbiviz` file and import it into Power BI like any other custom visual. This is how you share and reuse your visual.

In this lesson, you'll:

1. Fill out metadata in `pbiviz.json`
2. Build the `.pbiviz` package
3. Import it into Power BI Desktop
4. Use it with your data
5. Store it for reuse

---

## Step 1 - Stop the dev server

In the terminal where `pbiviz start` is running, press **Ctrl+C** to stop the local server.

```
^C
info    Server is stopping...
```

---

## Step 2 - Fill out `pbiviz.json`

This manifest file contains metadata about your visual (name, author, description, etc.). Open `pbiviz.json` in your `budgetTrafficLight` folder and update it:

```json
{
  "visual": {
    "name": "budgetTrafficLight",
    "displayName": "Budget Traffic Light",
    "guid": "budgetTrafficLight12345678901234567890",
    "visualClassName": "Visual",
    "description": "A KPI card visual for tracking burn rates and budget status with color-coded traffic light indicators.",
    "supportUrl": "https://github.com/yourusername/pbi-custom-visual-training",
    "gitHubUrl": "https://github.com/yourusername/pbi-custom-visual-training"
  },
  "author": {
    "name": "Your Name or Team",
    "email": "you@example.com"
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
```

### Key fields to update:

| Field | What it means | Example |
|-------|---------------|---------|
| `visual.displayName` | What users see in Power BI | "Budget Traffic Light" |
| `visual.description` | Brief description of the visual | "A KPI card visual..." |
| `visual.guid` | Unique ID (change the numbers) | "budgetTrafficLight..." |
| `author.name` | Your name or team | "Jane Doe" or "Analytics Team" |
| `author.email` | Contact email | "jane@company.com" |
| `version` | Package version (4 parts required) | "1.0.0.0" |
| `supportUrl` | Where to report issues | GitHub repo URL |
| `gitHubUrl` | GitHub repo URL | GitHub repo URL |

**Why the GUID?** Power BI uses this to uniquely identify your visual. If you build multiple visuals, each needs a different GUID. You can use an online [GUID generator](https://www.guidgenerator.com/) or just use the pattern `budgetTrafficLight` + some random numbers.

**Why 4-part version?** Power BI requires versions in the format `X.Y.Z.W` (e.g., `1.0.0.0`). Don't use `1.0` or `1.0.0` - it will fail.

---

## Step 3 - Build the `.pbiviz` package

In your terminal, in the `budgetTrafficLight` folder, run:

```bash
pbiviz package
```

You'll see output like:

```
info    Packaging visual...
info    Creating visual package successful
info    Output: dist/budgetTrafficLight.pbiviz
```

A new file `budgetTrafficLight.pbiviz` has been created in the `dist/` folder. This is your packaged visual!

**What to do if it fails:**
- Check that all required fields in `pbiviz.json` are filled in (name, email, description, etc.)
- Make sure `version` is in the 4-part format: `X.Y.Z.W`
- Verify `capabilities.json` exists and is valid JSON
- Check the error message closely - it usually tells you exactly what's missing

---

## Step 4 - Import the visual into Power BI Desktop

1. **Open Power BI Desktop**
2. **Open a report** (create a new blank one or use an existing one)
3. **Go to the Visualizations pane** (the panel on the right)
4. **Click the three dots** (...) at the bottom of the pane
5. Select **"Import a custom visual"** or **"Import from a file"**
6. A file browser opens - navigate to:
   ```
   budgetTrafficLight/dist/budgetTrafficLight.pbiviz
   ```
7. Click **Open**
8. A confirmation dialog appears - click **Add** or **Import**

You should see a new custom visual icon appear at the **bottom of the Visualizations pane**. It might take a moment to load.

---

## Step 5 - Use the visual with your data

1. **Load your demo data** (see Lesson 03, Step 6)
2. **Click the new visual icon** in the Visualizations pane (it's the one you just imported)
3. **Map your data:**
   - Drag **Category** to the **Category** field
   - Drag **BurnRate** to the **Burn Rate** field
4. The visual should render your KPI cards!

---

## Step 6 - Store your `.pbiviz` file

Keep your `.pbiviz` file safe. You can:

**Option A: Save it in a shared folder**
```
\\network\shared\CustomVisuals\budgetTrafficLight.pbiviz
```

**Option B: Upload it to a GitHub release**
1. Create a GitHub repo for this visual
2. Create a new Release
3. Attach the `.pbiviz` file

**Option C: Email it or add to a shared drive**
- The `.pbiviz` file is portable - you can email it, add it to OneDrive, etc.
- Anyone can import it the same way you did in Step 4

---

## Step 7 - Share with teammates

To share your visual with others:

1. Give them the `.pbiviz` file
2. They open Power BI Desktop
3. They go to **Visualizations** → **...** → **Import a custom visual**
4. They select your `.pbiviz` file
5. Done! They can now use your visual

**Note:** This only works in Power BI Desktop. To share visuals in Power BI Service (cloud), you'd need to publish them to the organizational store or AppSource, which is more advanced.

---

## Updating your visual

If you want to make changes and release a new version:

1. **Make your code changes** (in `visual.ts`, `visual.less`, etc.)
2. **Update the version** in `pbiviz.json` (e.g., from `1.0.0.0` to `1.1.0.0`)
3. **Run `pbiviz package` again**
4. **Import the new `.pbiviz` file** the same way as before
5. Power BI will see it as an update and may prompt you to replace the old version

---

## Troubleshooting packaging

### Error: "Packaging visual failed"

**Check the console output** for clues. Common issues:

1. **Missing required fields in `pbiviz.json`**
   - Make sure `author.name`, `author.email`, `visual.description` are all filled in
   - No empty strings - use real values

2. **Version format wrong**
   - Must be `X.Y.Z.W` (e.g., `1.0.0.0`)
   - Not `1.0` or `1.0.0`

3. **Invalid JSON in `capabilities.json`**
   - Open it and check for missing commas, quotes, etc.
   - Use an online JSON validator if unsure

4. **TypeScript compilation errors**
   - Check the console output - it should show which line has the error
   - Common issues: missing semicolons, typos in variable names

### Error: "Import failed" in Power BI

1. **Make sure developer mode is on** (Lesson 02, Step 3)
2. **Try restarting Power BI**
3. **Check that the `.pbiviz` file isn't corrupted**
   - Try packaging again and re-importing
4. **Check Power BI version**
   - Very old versions may not support some features
   - Update Power BI if possible

---

## What's next?

You now have a **fully packaged, shareable custom visual**! 

Next steps:

1. **Lesson 05 - Next Steps & Resources** for ideas on extending this visual
2. **Deploy it** - Email it to colleagues, store it, or add it to a shared repo
3. **Get feedback** - Let people use it and iterate based on what they want
4. **Build more** - Once you've mastered this one, try building a visual for another use case

Congratulations! You've built, packaged, and deployed a custom Power BI visual. That's a real achievement.
