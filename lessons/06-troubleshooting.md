---
layout: default
title: 06 - Troubleshooting Guide
---

# 06 - Troubleshooting Guide

This page covers common issues you might encounter when building custom Power BI visuals and how to solve them.

---

## Environment & Setup Issues

### Node.js / npm problems

#### Q: "Command not found" for `node` or `npm`

**Solutions:**
1. **Completely close and restart your terminal** - PATH updates don't take effect until you do
2. **Restart your computer** - On Windows, this refreshes environment variables
3. **Verify Node.js installed:**
   ```bash
   # Windows
   where node
   
   # Mac/Linux
   which node
   ```
4. **Reinstall Node.js:**
   - Go to [nodejs.org](https://nodejs.org)
   - Download the LTS version
   - Run the installer
   - Restart your terminal and try again

---

#### Q: `npm` version is very old (e.g., 4.x)

**Solution:**
Node.js installer bundles npm. Update Node.js to get a newer npm:
1. Download latest LTS from [nodejs.org](https://nodejs.org)
2. Run the installer (it upgrades Node and npm together)
3. Restart your terminal
4. Verify: `npm -v` should show 6.x or newer

---

#### Q: Permission denied when running `npm install -g`

**Solution (recommended for Mac/Linux):**

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

Then add this line to your shell profile:
- **Mac:** `~/.zshrc` or `~/.bash_profile`
- **Linux:** `~/.bashrc`

Add this line:
```bash
export PATH=~/.npm-global/bin:$PATH
```

Then restart your terminal.

**Alternative (less safe, but works):**
```bash
sudo npm install -g powerbi-visuals-tools
```

---

### Power BI Developer Mode

#### Q: Developer visual icon doesn't appear in Visualizations pane

**Checklist:**
1. Go to **File** → **Options and settings** → **Options** (Windows) or **Preferences** (Mac)
2. Click **Preview features** in the left sidebar
3. Make sure **"Developer visual"** is checked
4. Click **OK**
5. **Completely close Power BI** (not just minimize)
6. **Reopen Power BI**

If still missing:
- **Mac only:** Try restarting your computer entirely
- Check your Power BI version - very old versions (before mid-2022) don't support developer visuals
- If you're on a corporate network, contact IT - they may have disabled preview features

---

#### Q: Developer mode worked before, now it doesn't

**Solutions:**
1. **Restart Power BI** completely
2. **Check if preview features were disabled:**
   - Go back to **File** → **Options** → **Preview features**
   - Re-check "Developer visual"
3. **Update Power BI:**
   - Go to **File** → **Account** → **Check for updates**
   - Install any available updates
4. **Last resort:** Reinstall Power BI
   - Uninstall via Windows Settings or Mac App Store
   - Reinstall from [powerbi.microsoft.com](https://powerbi.microsoft.com)

---

## Project Scaffolding Issues

### Q: `pbiviz new` command fails

**Solutions:**

1. **Verify `pbiviz` is installed:**
   ```bash
   pbiviz --version
   ```
   If this fails, reinstall:
   ```bash
   npm install -g powerbi-visuals-tools
   ```

2. **Check folder permissions:**
   - Make sure you have write permission to the folder
   - Try creating the project in your home directory (`~/my-visuals/`)

3. **Folder name issues:**
   - Use lowercase names with no spaces: `myVisual` or `my-visual`
   - Not `My Visual` or `MyVisual123!`

4. **Existing folder:**
   - If a folder with that name already exists, use a different name
   - Or delete the old folder first: `rm -rf budgetTrafficLight`

---

### Q: `npm install` fails after scaffolding

**Solutions:**

1. **Check internet connection** - npm needs to download packages
2. **Clear npm cache:**
   ```bash
   npm cache clean --force
   ```
3. **Try again:**
   ```bash
   npm install
   ```
4. **If still failing, check for error details:**
   ```bash
   npm install --verbose
   ```

---

## Development & Code Issues

### TypeScript/Code Errors

#### Q: `pbiviz start` fails with TypeScript compilation error

**Example error:**
```
error TS2339: Property 'xyz' does not exist on type 'abc'
```

**Solutions:**
1. **Read the error carefully** - it tells you the file and line number
2. **Check for typos** - especially property names and variable names
3. **Check the data types** - make sure you're using the right type
4. **Look at the example code:**
   - Lesson 03 provides working `visual.ts` code
   - Compare your code line-by-line with the example
5. **Ask for help:**
   - Search the error message on Google/Stack Overflow
   - Check the [Power BI visuals docs](https://microsoft.github.io/PowerBI-visuals/)

---

#### Q: Visual compiles but doesn't render anything

**Checklist:**
1. **Check the browser console:**
   - In Power BI, press **F12**
   - Go to **Console** tab
   - Look for red errors
2. **Check Power BI visual error:**
   - Look at the visual in Power BI - does it show an error message?
3. **Verify data is mapped:**
   - In Power BI, make sure you've mapped fields to the data roles
   - Check that column names match what you expect
4. **Add debug output:**
   ```typescript
   console.log("Categories:", categories.values);
   console.log("Values:", values.values);
   ```
   - Restart the dev server
   - Check what data is being received
5. **Check `capabilities.json`:**
   - Make sure the data roles match your `visual.ts` code
   - Field names should match

---

#### Q: "Cannot find module X"

**Example:**
```
Error: Cannot find module 'powerbi-visuals-api'
```

**Solutions:**
1. **Run `npm install` again:**
   ```bash
   npm install
   ```
2. **Check `package.json`** - the module should be listed in `dependencies`
3. **Delete and reinstall:**
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

---

### Data & Binding Issues

#### Q: Cards don't show up or data is undefined

**Troubleshooting steps:**

1. **Check data mapping:**
   - In Power BI, go to the Visualizations pane
   - Make sure you've mapped fields to **Category** and **Burn Rate**
   - If fields aren't showing, check your data is loaded

2. **Check `capabilities.json`:**
   - Make sure the data role names match (case-sensitive!)
   - Example: if you defined `"name": "Category"`, code should use `"Category"`

3. **Add logging to debug:**
   ```typescript
   console.log("dataView:", dataView);
   console.log("categories:", dataView?.categorical?.categories);
   ```

4. **Restart Power BI:**
   - Close Power BI completely
   - Reopen it
   - Reload your report

---

#### Q: Data looks wrong or truncated

**Solutions:**
1. **Check your data in Power BI:**
   - Load the data without the custom visual
   - Use a built-in table visual to verify values are correct
2. **Check data types:**
   - Categories should be text
   - Values should be numbers
   - In Power BI, right-click a column → **Change data type** if needed
3. **Check for NULL values:**
   - Your code might not handle NULL values
   - Add a check: `if (value === null) { ... }`

---

## Packaging & Import Issues

### Q: `pbiviz package` fails

**Common errors:**

1. **"should have required property 'privileges'"**
   - In `capabilities.json`, add: `"privileges": []`
   - At the root level (after `dataViewMappings`)

2. **"Visual version should consist of 4 parts"**
   - In `pbiviz.json`, change: `"version": "1.0.0.0"`
   - (Must be X.Y.Z.W format)

3. **Missing author information**
   - In `pbiviz.json`, make sure these are filled in:
     - `author.name`
     - `author.email`
     - `visual.description`

4. **Invalid JSON in `pbiviz.json` or `capabilities.json`**
   - Open in VS Code
   - Check for red squiggly lines (syntax errors)
   - Look for missing commas, quotes, or brackets

---

### Q: Import fails in Power BI

#### Issue: "Failed to import visual" or similar error

**Solutions:**
1. **Make sure developer mode is on** (Lesson 02, Step 3)
2. **Try a fresh import:**
   - Delete the custom visual from the visuals list
   - Restart Power BI
   - Re-import the `.pbiviz` file
3. **Verify the file:**
   - Make sure `.pbiviz` exists at the expected path
   - Try re-packaging: `pbiviz package`
   - Try importing the newly created file
4. **Update Power BI:**
   - Go to **File** → **Account** → **Check for updates**
   - Install latest version

---

#### Issue: Visual imports but shows error when used

**Troubleshooting:**
1. **Press F12** in Power BI to open Developer Tools
2. **Go to Console tab**
3. **Look for error messages**
4. **Common issues:**
   - Missing data role mappings
   - TypeScript errors (check the original dev console output)
   - File path issues in the visual code

---

## Performance Issues

### Q: Visual is slow or freezes Power BI

**Common causes & fixes:**

1. **Too much data:**
   - Custom visuals work best with < 10,000 rows
   - If you have more, use a filter in Power BI
   - Consider implementing data sampling in your visual

2. **Inefficient loops:**
   - If you loop through data multiple times, combine into one loop
   - Example: Don't loop to find min, then loop to find max - do both in one pass

3. **Complex CSS animations:**
   - Reduce animations or remove them: `transition: none`
   - Use `will-change` sparingly

4. **Memory leaks:**
   - Make sure you clean up in the update method
   - Don't create new DOM elements without removing old ones
   - (The example in Lesson 03 does `container.innerHTML = ""` to avoid this)

---

## Still stuck?

If you can't find your issue here:

1. **Search online:**
   - Google the error message
   - Check [Stack Overflow](https://stackoverflow.com/questions/tagged/power-bi)
   - Check [Power BI Community Forums](https://community.powerbi.com/)

2. **Check official docs:**
   - [Microsoft Power BI Visuals Documentation](https://microsoft.github.io/PowerBI-visuals/)
   - [Power BI Dev Samples](https://github.com/microsoft/PowerBI-visuals)

3. **Ask for help:**
   - Share your error message
   - Share your code (if possible)
   - Explain what you've already tried

---

## Glossary of common terms

| Term | What it means |
|------|---------------|
| **`.pbiviz` file** | A packaged custom visual (compressed file format) |
| **`pbiviz start`** | Command to start local dev server (live reload) |
| **`pbiviz package`** | Command to build a `.pbiviz` package for sharing |
| **Data role** | An input your visual accepts (e.g., Category, Value) |
| **Data view** | The actual data passed to your visual at runtime |
| **Developer mode** | Special mode in Power BI that lets you test local visuals |
| **TypeScript** | A superset of JavaScript with type checking |
| **`.less` file** | A CSS-like styling file (compiles to CSS) |
| **`capabilities.json`** | Configuration file that defines your visual's data inputs |

---

Good luck! Don't hesitate to experiment and learn by doing.
