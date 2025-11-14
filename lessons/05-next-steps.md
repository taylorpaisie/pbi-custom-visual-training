
---

## 9. `lessons/05-next-steps.md` – Next steps & gotchas

Create `lessons/05-next-steps.md`:

```markdown
---
layout: default
title: 05 – Next Steps & Resources
---

# 05 – Next Steps & Resources

## Common gotchas

**1. `should have required property 'privileges'`**

- Add `"privileges": []` to your `capabilities.json` root.
- If you need web access or export, you can add specific privileges later.

---

**2. `Visual version should consist of 4 parts`**

- In `pbiviz.json`, use a 4-part version string, e.g. `"version": "1.0.0.0"`.

---

**3. Packaging errors about author or URLs**

- Fill in:
  - `author.name`
  - `author.email`
  - `visual.description`
  - `visual.supportUrl`
  - `visual.gitHubUrl`

---

**4. No format options in the Format pane**

- You need to define objects in `capabilities.json` and hook them up via `settings.ts`.
- This is a great “advanced” topic once everyone is comfortable with the basics.

---

## Where to go next

Ideas for extending this training:

- Rewrite the visual using **React** and a component library.
- Use **SVG** or **D3** for richer graphics.
- Add **formatting options** so users can:
  - Change thresholds
  - Adjust colors
  - Toggle labels on/off
- Explore:
  - Additional data roles (tooltips, secondary measures)
  - Integration with theming / report styling

---

## Recommended practice

- Build a second visual that solves a real problem your team has (however small).
- Keep the scope tiny but complete:
  - One clear purpose
  - One or two data roles
  - A simple, obvious visual output

Ship it, version it, and suddenly you’re “the custom visual person” on the team.
