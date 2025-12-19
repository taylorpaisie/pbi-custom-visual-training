---
layout: default
title: 05 - Next Steps & Resources
---

# 05 - Next Steps & Resources

Congratulations! You've built, packaged, and deployed a custom Power BI visual. This lesson covers what to do next and provides resources for deepening your skills.

---

## Next Milestones

### Short term (this week)

1. **Deploy your visual**
   - Share the `.pbiviz` file with colleagues
   - Get feedback on the design and functionality
   - Document where to find the latest version

2. **Try a small enhancement**
   - Change the color thresholds (in `visual.ts`)
   - Adjust card sizes (in `visual.less`)
   - Add a title or summary text
   - Bump the version to `1.1.0.0` and re-package

3. **Test with real data**
   - Load your actual project data into Power BI
   - Use the visual with your real burn rates
   - Identify any tweaks needed

### Medium term (next month)

1. **Build a second visual**
   - Identify another small problem your team has
   - Keep it simple: one visual type, 1-2 data roles
   - Apply what you learned here but adapt it

2. **Add formatting options**
   - Let users change colors in the Format pane
   - Let users adjust thresholds
   - Let users toggle labels on/off
   - (See "Advanced Topics" below)

3. **Improve the UI**
   - Add icons or emojis to status labels
   - Use better fonts and spacing
   - Add hover tooltips
   - Make it more visually appealing

### Long term (next quarter)

1. **Become the custom visual expert on your team**
   - Document your visuals
   - Train others on how to use them
   - Maintain a repo of team visuals

2. **Explore enterprise scenarios**
   - Publish to AppSource (Microsoft's visual marketplace)
   - Create visuals for multiple teams
   - Build a visual framework for your organization

---

## Advanced Topics

### Adding Format Options (Settings)

Let users customize your visual in the **Format** pane. For example:

- Change threshold values (currently hardcoded as 0.8 and 1.0)
- Pick custom colors
- Toggle labels on/off

**How it works:**
1. Define "objects" in `capabilities.json`
2. Implement handlers in `settings.ts` (already scaffolded)
3. Access settings in `visual.ts` and apply them

**Example:** Customizable thresholds

```json
// In capabilities.json
"objects": {
  "thresholds": {
    "displayName": "Thresholds",
    "properties": {
      "yellowThreshold": {
        "displayName": "Yellow threshold",
        "type": { "numeric": true }
      },
      "redThreshold": {
        "displayName": "Red threshold",
        "type": { "numeric": true }
      }
    }
  }
}
```

Then in `visual.ts`, read these values and adjust the color logic.

**Resources:**
- [Power BI Objects & Properties](https://microsoft.github.io/PowerBI-visuals/docs/concepts/objects-and-properties/)
- [Settings example in samples](https://github.com/microsoft/PowerBI-visuals/tree/master/samples)

---

### Using React or D3

The Budget Traffic Light visual uses vanilla HTML + CSS + TypeScript. You can upgrade to:

**React:**
- Better component structure
- Easier state management
- Reusable UI components
- Larger bundle size

**D3:**
- Rich data visualizations
- Advanced charting features
- Steep learning curve but powerful
- Good for custom shapes and animations

**Getting started:**
- Look at the [Power BI Samples repo](https://github.com/microsoft/PowerBI-visuals) for examples
- Search for "Power BI React visual" or "Power BI D3 visual"

---

### Multiple Data Roles

The current visual has:
- 1 Category (grouping)
- 1 Value (measure)

You can add more:

**Secondary Value:**
```json
{
  "name": "SecondaryValue",
  "kind": "Measure",
  "displayName": "Target"
}
```

Then in `visual.ts`, compare actual vs. target and show variance.

**Tooltip Data:**
```json
{
  "name": "Tooltip",
  "kind": "Measure",
  "displayName": "Additional Info"
}
```

Then show tooltip on hover with extra details.

**Resources:**
- [Data Roles in Power BI Visuals](https://microsoft.github.io/PowerBI-visuals/docs/concepts/data-roles/)

---

### SVG & Canvas Graphics

Instead of HTML cards, use:

**SVG:** Vector graphics (shapes, lines, text)
- Good for: Custom icons, diagrams, network graphs
- Example: Draw circles with custom sizes and colors

**Canvas:** Bitmap graphics (pixels)
- Good for: Heat maps, particle effects, complex animations
- Example: Draw a custom gauge needle

**Getting started:**
- Learn basic SVG: [MDN SVG Tutorial](https://developer.mozilla.org/en-US/docs/Web/SVG)
- Canvas: [HTML5 Canvas API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)

---

### Responsive Design

The current grid is responsive (adjusts to different screen sizes), but you can improve:

1. **Mobile-friendly:**
   - Test on tablets and phones
   - Adjust font sizes for small screens
   - Consider a list view vs. grid for mobile

2. **Dynamic card sizing:**
   - Smaller cards if many items
   - Larger cards if few items
   - Adjust based on viewport

3. **Accessibility:**
   - Use semantic HTML (`<button>`, `<label>`, etc.)
   - Add keyboard navigation
   - Ensure color isn't the only indicator (add text labels)
   - Test with screen readers

**Resources:**
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [Accessible SVG](https://www.smashingmagazine.com/2021/05/accessible-svgs-inclusiveness-beyond-patterns/)

---

## Common Gotchas & Solutions

### Error: "should have required property 'privileges'"

```json
// In capabilities.json, add at the root level:
"privileges": []
```

If you need web access or export, add:
```json
"privileges": [
  { "name": "webAccess" },
  { "name": "exportContent" }
]
```

---

### Error: "Visual version should consist of 4 parts"

In `pbiviz.json`, version must be `X.Y.Z.W`:
```json
"version": "1.0.0.0"   // Good
// "version": "1.0"     // Bad
// "version": "1.0.0"   // Bad
```

---

### Visual doesn't update after changing code

1. Stop dev server: **Ctrl+C**
2. Run `npm install` again (just to be safe)
3. Run `pbiviz start`
4. Refresh Power BI

If that doesn't work:
- Close Power BI completely
- Restart the dev server
- Reopen Power BI

---

### My visual works in dev mode but fails when packaged

**Common cause:** Missing data validations

Add checks in `visual.ts`:
```typescript
if (!dataView || !dataView.categorical) {
  this.container.innerHTML = "No data";
  return;
}

if (categories.values.length === 0) {
  this.container.innerHTML = "No categories";
  return;
}
```

This prevents crashes on edge cases (empty data, null values, etc.).

---

## Learning Resources

### Official Microsoft Documentation

- [Power BI Visuals API Reference](https://microsoft.github.io/PowerBI-visuals/)
- [Power BI Visuals Samples](https://github.com/microsoft/PowerBI-visuals)
- [Power BI Visuals Playground](https://microsoft.github.io/PowerBI-visuals/playground/)

### Community & Support

- [Power BI Community Forums](https://community.powerbi.com/)
- [Stack Overflow - power-bi tag](https://stackoverflow.com/questions/tagged/power-bi)
- [GitHub Issues - PowerBI-visuals](https://github.com/microsoft/PowerBI-visuals/issues)

### Learning Paths

- [Microsoft Learn - Power BI Developer](https://learn.microsoft.com/en-us/power-bi/developer/visuals/)
- [Pluralsight - Power BI Development](https://www.pluralsight.com/) (if you have a subscription)

### Books & Articles

- "Learning Power BI" by Jeremey Arnold (covers visuals)
- Blog posts on Power BI development (search Medium, Dev.to, etc.)

---

## Practice Projects

### Project 1: KPI Gauge

Build a visual that shows:
- A numeric value with a needle/pointer
- Color-coded based on good/bad ranges
- Works like a speedometer

**Data:** Category + Value

---

### Project 2: Comparison Bar

Show two values side-by-side:
- Bar for "Planned"
- Bar for "Actual"
- Color based on variance

**Data:** Category + Planned + Actual

---

### Project 3: Timeline

Show a horizontal timeline:
- Events on a line
- Milestones marked
- Colors for status (on-time, delayed, completed)

**Data:** Event Name + Date + Status

---

### Project 4: Heat Map

Show intensity with colors:
- Grid cells colored by value
- Darker = higher value
- Good for correlations or status matrices

**Data:** Row + Column + Intensity

---

## Sharing Your Visual

### With your organization

1. **Via email:** Send the `.pbiviz` file
2. **Via OneDrive/SharePoint:** Upload and share link
3. **Via GitHub:** Create a public repo, release the file
4. **Via Power BI Admin Portal:** Deploy to organization (if you have access)

### With the world

1. **Submit to AppSource:**
   - Microsoft's official marketplace for Power BI visuals
   - Requires certification
   - See [AppSource submission guide](https://learn.microsoft.com/en-us/power-bi/developer/visuals/submission-guidelines/)

2. **Open source on GitHub:**
   - Share code + `.pbiviz` file
   - Let others contribute improvements
   - Build community around your visual

---

## What to Track

As you build more visuals, keep notes:

1. **Time investment:** How long did each visual take?
2. **User feedback:** What do people ask for?
3. **Bugs found:** What broke in production?
4. **Improvements:** What would you do differently next time?

This becomes a playbook for future projects.

---

## Summary

You're now equipped to:

✅ **Scaffold** a custom visual  
✅ **Code** rendering logic in TypeScript  
✅ **Style** visuals with CSS  
✅ **Define** data roles and mappings  
✅ **Package** a `.pbiviz` file  
✅ **Deploy** to Power BI Desktop  
✅ **Troubleshoot** common issues  

From here, you can:

- Enhance the Budget Traffic Light visual further
- Build completely new visuals for your team
- Contribute to open-source Power BI projects
- Become an expert in custom Power BI development

**Good luck! Build amazing things.**

---

## Questions?

If you need help:

1. Check [Lesson 06 - Troubleshooting Guide](lessons/06-troubleshooting.html)
2. Search the [Power BI Community Forums](https://community.powerbi.com/)
3. Ask on [Stack Overflow](https://stackoverflow.com/questions/tagged/power-bi)

Happy building! 🚀
