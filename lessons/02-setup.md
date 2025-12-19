---
layout: default
title: 02 - Environment Setup
---

# 02 - Environment Setup

Before we start writing code, we need to set up three key things:

1. Install the Power BI visuals tools (`pbiviz`) and Node.js
2. Verify everything is working correctly
3. Enable developer mode in Power BI Desktop
4. Create a working folder for the custom visual

Once this is done, you'll be ready for the lab in Lesson 03.

---

## Step 1 - Install Node.js and npm

If you already use Node.js for other projects, you can skip to Step 2.

Otherwise:

1. Go to [nodejs.org](https://nodejs.org) and download the **LTS** (Long Term Support) installer for your OS
2. Run the installer and accept the defaults
3. Restart your terminal completely (close and reopen)

**Why LTS?** Microsoft officially recommends Node.js + npm as the base for Power BI visual development. The LTS version is stable and well-tested.

To verify the installation in a terminal (PowerShell, Command Prompt, WSL, Terminal on Mac, etc.):

```bash
node -v
npm -v
```

You should see version numbers (e.g., `v18.17.0` and `9.8.1`). If you get "command not found":
- Make sure you restarted your terminal *completely* 
- On Windows, try a fresh PowerShell window
- If it still doesn't work, restart your computer and try again

---

## Step 2 - Install `pbiviz` globally

The `pbiviz` command-line tool is what you'll use to scaffold, develop, and package custom visuals.

```bash
npm install -g powerbi-visuals-tools
```

This downloads and installs the Power BI Visuals Tools globally so you can use the `pbiviz` command from anywhere.

Verify the installation:

```bash
pbiviz --version
```

You should see a version number (e.g., `5.3.0` or higher).

**What if it's not found?**
- On **Windows**: Open a new PowerShell window. The PATH environment variable may not have updated.
- On **Mac/Linux**: Try `npm list -g powerbi-visuals-tools` to confirm it installed, then run `source ~/.bashrc` or restart your terminal.
- If still stuck, run `npm install -g powerbi-visuals-tools` again.

---

## Step 3 - Enable Developer Mode in Power BI Desktop

Developer mode lets you test your custom visual locally in Power BI before packaging it. This is essential for the lab.

### Windows

1. Open **Power BI Desktop**
2. Go to **File** → **Options and settings** → **Options**
3. In the left sidebar, scroll down and click **Preview features**
4. Check the box for **"Developer visual"** (might be labeled "Enable developer visual" in some versions)
5. Click **OK**
6. **Restart Power BI Desktop completely** (close and reopen)

### Mac

1. Open **Power BI Desktop**
2. Click **Power BI Desktop** in the top menu bar → **Preferences**
3. Select the **Preview features** tab
4. Check **"Developer visual"**
5. Click **Apply**
6. **Restart Power BI Desktop completely**

### Verify It Worked

1. Open Power BI Desktop and create a new blank report
2. Go to the **Visualizations** pane on the right
3. Look for a **developer visual icon** that looks like code brackets `<>` 
4. If you see it, you're good to go!
5. If not, restart Power BI and check your Preview features again

---

## Step 4 - Create a working folder

Pick a location where you'll build custom visuals and stay organized:

```bash
# Create a folder (adjust the path as needed for your system)
mkdir ~/pbi-custom-visuals
cd ~/pbi-custom-visuals
```

This folder will be your "home base" for the next lesson. You'll scaffold the `budgetTrafficLight` project here.

---

## Checklist Before Moving On

Before you start Lesson 03, make sure:

- [ ] Node.js installed (`node -v` shows a version)
- [ ] npm installed (`npm -v` shows a version)
- [ ] `pbiviz` installed (`pbiviz --version` shows a version)
- [ ] Developer mode **enabled** in Power BI Desktop
- [ ] Developer visual icon appears in the Visualizations pane
- [ ] Working folder created (~/pbi-custom-visuals or similar)

---

## Troubleshooting Setup Issues

### Issue: `pbiviz` command not found after installation

**Windows**
- Open a fresh PowerShell window (not the one where you ran npm install)
- Try the command again
- If still not found, restart your computer

**Mac/Linux**
- Run `npm list -g powerbi-visuals-tools` to verify it's installed
- Try `source ~/.bashrc` to refresh your PATH
- Restart your terminal window completely

**Last resort**
- Run `npm install -g powerbi-visuals-tools` again
- Wait for it to complete fully
- Restart your terminal and try again

---

### Issue: Power BI doesn't show the developer visual icon

**Check Preview features again**
1. Go to **File** → **Options and settings** → **Options** (Windows) or **Preferences** (Mac)
2. Make sure **"Developer visual"** is actually checked
3. Click **OK** or **Apply**
4. Fully close Power BI (not just minimize) and reopen it

**Mac-specific**
- Try restarting your computer entirely
- Make sure you're on a recent version of Power BI (July 2022 or later supports developer mode)

**Windows-specific**
- If you're on a corporate network, your admin may have disabled preview features
- Contact your IT team to confirm preview features are allowed

---

### Issue: Node.js or npm versions are very old

Check your current versions:

```bash
node -v
npm -v
```

If `npm` is older than 6.x, upgrade Node.js:

1. Go to [nodejs.org](https://nodejs.org) and download the latest LTS
2. Run the installer (it will upgrade even if Node is already installed)
3. Restart your terminal completely
4. Verify with `node -v` and `npm -v` again

---

### Issue: Permission errors when running `npm install -g`

If you see errors like "permission denied" on Mac/Linux:

**Option A (Recommended): Fix npm permissions**

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

Then add this line to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.):

```bash
export PATH=~/.npm-global/bin:$PATH
```

**Option B: Use sudo (Less recommended, but works)**

```bash
sudo npm install -g powerbi-visuals-tools
```

Then use `sudo pbiviz` when needed.

---

## Next Steps

Once you've completed all the checklist items, you're ready for **Lesson 03 - Lab: Budget Traffic Light Visual**. In that lesson, you'll scaffold your first custom visual and start implementing it.

See you there!
