
---

## `lessons/02-setup.md`

```markdown
---
layout: default
title: 02 – Environment Setup
---

# 02 – Environment Setup

Before we start writing code, we need to:

1. Install the Power BI visuals tools (`pbiviz`)  
2. Verify Node.js and npm are working  
3. Enable developer mode in Power BI Desktop  
4. Create a working folder for the custom visual

Once this is done, you’ll be ready for the lab in Lesson 03.

---

## Step 1 – Install Node.js and npm

If you already use Node.js for other projects, you can skip to Step 2.

Otherwise:

1. Go to the Node.js website and download the **LTS** installer for your OS.
2. Run the installer and accept the defaults.
3. Restart your terminal when done.

Microsoft’s official environment setup docs recommend Node.js + npm as the base for Power BI visual development. :contentReference[oaicite:4]{index=4}  

To verify in a terminal (PowerShell, Command Prompt, WSL, etc.):

```bash
node -v
npm -v
