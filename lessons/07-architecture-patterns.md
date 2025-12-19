---
layout: default
title: 07 - Code Architecture & Organization Patterns
---

# 07 - Code Architecture & Organization Patterns

The Budget Traffic Light visual from Lesson 03 works great as a simple example, but real-world visuals get complex fast. This guide shows you **how to organize code** as your visuals grow, using the traffic light visual as a case study.

**Time to read:** 20 minutes  
**Difficulty:** Intermediate to Advanced  
**Prerequisites:** Complete Lesson 03 (understand basic visual structure)

---

## The Problem: Code Grows, Becomes Hard to Manage

Let's say you want to enhance the traffic light visual:

- Add color customization (let users pick colors)
- Add hover tooltips with extra data
- Add animations when cards load
- Add a search/filter feature
- Support for 10,000+ rows with pagination
- Unit tests for the color logic

If you put all this in `visual.ts`, it becomes a 1,000+ line file that's:
- Hard to read
- Hard to test
- Hard to reuse
- Hard to maintain
- Hard to debug

**The solution:** Split code into smaller, focused pieces.

---

## Architecture Patterns

### Pattern 1: Separation of Concerns (SoC)

**Idea:** Each class or function does ONE thing well.

**Example:** Instead of one `Visual` class doing everything:

```
visual.ts           (Main class, coordinates components)
├── dataProcessor.ts (Extracts and validates data)
├── cardRenderer.ts  (Renders card HTML)
├── colorLogic.ts    (Determines colors)
└── eventHandlers.ts (Click/hover events)
```

**Benefits:**
- ✅ Test each piece independently
- ✅ Reuse logic in other visuals
- ✅ Find bugs faster
- ✅ New developers understand code faster

---

### Pattern 2: Model-View-Controller (MVC)

**Idea:** Separate data (Model), display (View), and logic (Controller).

```
Visual (Controller)
  ├── reads DataView
  ├── calls Model.process()
  └── tells View.render()

CardData (Model)
  ├── validates input
  ├── calculates derived values (color, status)
  └── handles business logic

CardView (View)
  ├── creates HTML
  ├── applies styles
  └── handles user interactions
```

**When to use:** Medium-sized visuals with data processing and complex rendering.

---

### Pattern 3: Service Layer Pattern

**Idea:** Create reusable "services" that handle specific tasks.

```
Visual
  ├── colorService.getColor(burnRate)
  ├── dataService.parseDataView(dataView)
  ├── renderService.createCard(data)
  └── eventService.attachHandlers(element)
```

**Benefits:**
- ✅ Services can be tested independently
- ✅ Easy to swap implementations
- ✅ Services can be reused across visuals
- ✅ Clear naming (exactly what each does)

---

## Real Example: Refactoring the Traffic Light Visual

### Before: Everything in visual.ts (500+ lines)

```typescript
// BAD: Everything mixed together
export class Visual implements IVisual {
  constructor(options) { ... }
  
  update(options) {
    // Data extraction
    const categories = options.dataViews[0].categorical.categories[0].values;
    const values = options.dataViews[0].categorical.values[0].values;
    
    // Color logic
    for (let i = 0; i < categories.length; i++) {
      let color;
      if (values[i] < 0.8) {
        color = '#2ecc71';
      } else if (values[i] <= 1.0) {
        color = '#f39c12';
      } else {
        color = '#e74c3c';
      }
      
      // Rendering
      const card = document.createElement('div');
      card.style.borderLeftColor = color;
      card.innerHTML = `...`;
      
      // Event handling
      card.addEventListener('click', (e) => {
        // ... 50 lines of click logic
      });
      
      this.container.appendChild(card);
    }
  }
}
```

**Problems:**
- ❌ Hard to test color logic
- ❌ Hard to change rendering
- ❌ Can't reuse color logic in another visual
- ❌ 500+ line file
- ❌ Mixed responsibilities

---

### After: Organized Architecture

**File structure:**

```
src/
├── visual.ts              (Main Visual class, orchestrates)
├── services/
│   ├── dataService.ts     (Parse and validate data)
│   ├── colorService.ts    (Color logic - testable!)
│   ├── renderService.ts   (Create HTML elements)
│   └── eventService.ts    (Handle interactions)
├── models/
│   ├── CardData.ts        (Data structure for a card)
│   └── TrafficLightConfig.ts (Configuration)
└── utils/
    └── validators.ts      (Input validation)
```

---

## Code Examples: Refactored Approach

### 1. ColorService (Reusable!)

**File:** `src/services/colorService.ts`

```typescript
/**
 * Determines traffic light color based on burn rate.
 * This is pure logic - no DOM access, easy to test!
 */
export class ColorService {
  private yellowThreshold: number = 0.8;
  private redThreshold: number = 1.0;

  /**
   * Get color for a given burn rate
   * @param burnRate - Value between 0 and 2 (e.g., 0.5, 1.2)
   * @returns CSS color code
   */
  public getColor(burnRate: number): string {
    if (burnRate < this.yellowThreshold) {
      return '#2ecc71'; // Green
    } else if (burnRate <= this.redThreshold) {
      return '#f39c12'; // Yellow
    } else {
      return '#e74c3c'; // Red
    }
  }

  /**
   * Get status label for display
   */
  public getStatus(burnRate: number): string {
    if (burnRate < this.yellowThreshold) {
      return 'On Track';
    } else if (burnRate <= this.redThreshold) {
      return 'Watch';
    } else {
      return 'Over Budget';
    }
  }

  /**
   * Get CSS class name for styling
   */
  public getStatusClass(burnRate: number): string {
    if (burnRate < this.yellowThreshold) {
      return 'status-green';
    } else if (burnRate <= this.redThreshold) {
      return 'status-yellow';
    } else {
      return 'status-red';
    }
  }

  /**
   * Allow custom thresholds (for format options later)
   */
  public setThresholds(yellow: number, red: number): void {
    this.yellowThreshold = yellow;
    this.redThreshold = red;
  }
}
```

**Why this is better:**
- ✅ Pure logic, no side effects
- ✅ Super easy to unit test
- ✅ Can be reused in other visuals
- ✅ Can add custom thresholds later

---

### 2. DataService (Handles extraction and validation)

**File:** `src/services/dataService.ts`

```typescript
import DataView = powerbiVisualsApi.DataView;

export interface CardData {
  category: string;
  value: number;
  index: number;
}

/**
 * Handles extracting and validating data from Power BI
 */
export class DataService {
  /**
   * Extract card data from Power BI DataView
   * @throws Error if data is invalid
   */
  public extractCardData(dataView: DataView): CardData[] {
    // Validate
    if (!dataView || !dataView.categorical) {
      throw new Error('No data available');
    }

    const categories = dataView.categorical.categories?.[0];
    const values = dataView.categorical.values?.[0];

    if (!categories || !values) {
      throw new Error('Missing required data roles');
    }

    // Extract
    const cards: CardData[] = [];
    for (let i = 0; i < categories.values.length; i++) {
      cards.push({
        category: String(categories.values[i]),
        value: Number(values.values[i]),
        index: i
      });
    }

    return cards;
  }

  /**
   * Validate a single card's data
   */
  public validateCard(card: CardData): boolean {
    return (
      card.category && 
      card.category.length > 0 &&
      typeof card.value === 'number' &&
      !isNaN(card.value)
    );
  }

  /**
   * Filter out invalid cards
   */
  public filterValidCards(cards: CardData[]): CardData[] {
    return cards.filter(card => this.validateCard(card));
  }
}
```

**Why this is better:**
- ✅ Data extraction is isolated
- ✅ Can validate and sanitize data
- ✅ Easy to test with mock data
- ✅ Can handle different data shapes

---

### 3. RenderService (Handles HTML creation)

**File:** `src/services/renderService.ts`

```typescript
import { CardData } from './dataService';
import { ColorService } from './colorService';

/**
 * Handles rendering cards to the DOM
 */
export class RenderService {
  private colorService: ColorService;

  constructor(colorService: ColorService) {
    this.colorService = colorService;
  }

  /**
   * Create a single card element
   */
  public createCard(data: CardData): HTMLElement {
    const card = document.createElement('div');
    card.className = `traffic-light-card ${this.colorService.getStatusClass(data.value)}`;
    card.setAttribute('data-index', String(data.index));

    // Header
    const header = document.createElement('div');
    header.className = 'card-header';
    header.textContent = data.category;
    card.appendChild(header);

    // Status
    const status = document.createElement('div');
    status.className = 'card-status';
    status.textContent = this.colorService.getStatus(data.value);
    card.appendChild(status);

    // Value
    const value = document.createElement('div');
    value.className = 'card-value';
    value.textContent = data.value.toFixed(2);
    card.appendChild(value);

    // Progress bar
    const barContainer = document.createElement('div');
    barContainer.className = 'card-bar-container';

    const bar = document.createElement('div');
    bar.className = 'card-bar';
    bar.style.width = `${Math.min(data.value * 100, 100)}%`;
    barContainer.appendChild(bar);
    card.appendChild(barContainer);

    return card;
  }

  /**
   * Create the grid container
   */
  public createGrid(cards: CardData[]): HTMLElement {
    const grid = document.createElement('div');
    grid.className = 'traffic-light-grid';

    cards.forEach(card => {
      const cardElement = this.createCard(card);
      grid.appendChild(cardElement);
    });

    return grid;
  }

  /**
   * Clear container and render grid
   */
  public render(container: HTMLElement, grid: HTMLElement): void {
    container.innerHTML = '';
    container.appendChild(grid);
  }
}
```

**Why this is better:**
- ✅ All DOM manipulation in one place
- ✅ Easy to update styling
- ✅ Can swap rendering methods (React, etc.)
- ✅ Testable (can check HTML output)

---

### 4. Clean Visual Class (Orchestrator)

**File:** `src/visual.ts`

```typescript
import IVisual = powerbiVisualsApi.extensibility.visual.IVisual;
import VisualConstructorOptions = powerbiVisualsApi.extensibility.visual.VisualConstructorOptions;
import VisualUpdateOptions = powerbiVisualsApi.extensibility.visual.VisualUpdateOptions;

import { DataService } from './services/dataService';
import { ColorService } from './services/colorService';
import { RenderService } from './services/renderService';

/**
 * Main Visual class - orchestrates the other services
 * Notice: Very clean and easy to understand!
 */
export class Visual implements IVisual {
  private host: powerbiVisualsApi.extensibility.IVisualHost;
  private container: HTMLElement;

  // Services
  private dataService: DataService;
  private colorService: ColorService;
  private renderService: RenderService;

  constructor(options: VisualConstructorOptions) {
    this.host = options.host;
    this.container = options.element;

    // Initialize services
    this.dataService = new DataService();
    this.colorService = new ColorService();
    this.renderService = new RenderService(this.colorService);
  }

  public update(options: VisualUpdateOptions): void {
    try {
      // Extract data
      const cards = this.dataService.extractCardData(options.dataViews[0]);

      // Validate data
      const validCards = this.dataService.filterValidCards(cards);

      if (validCards.length === 0) {
        this.container.innerHTML = '<p>No valid data</p>';
        return;
      }

      // Render
      const grid = this.renderService.createGrid(validCards);
      this.renderService.render(this.container, grid);

    } catch (error) {
      this.container.innerHTML = `<p>Error: ${error.message}</p>`;
      console.error(error);
    }
  }
}
```

**Why this is better:**
- ✅ Only 40 lines instead of 500!
- ✅ Reads like a story: extract → validate → render
- ✅ Easy to add features (just call more methods)
- ✅ Easy to debug (know which service failed)

---

## Testing: Why Organized Code Matters

With the refactored code, testing is straightforward:

### Test the ColorService

```typescript
import { ColorService } from '../services/colorService';

describe('ColorService', () => {
  let service: ColorService;

  beforeEach(() => {
    service = new ColorService();
  });

  test('burnRate < 0.8 returns green', () => {
    expect(service.getColor(0.5)).toBe('#2ecc71');
  });

  test('burnRate 0.8-1.0 returns yellow', () => {
    expect(service.getColor(0.9)).toBe('#f39c12');
  });

  test('burnRate > 1.0 returns red', () => {
    expect(service.getColor(1.2)).toBe('#e74c3c');
  });
});
```

**Notice:** No DOM, no Power BI API, no mocking complexity. Just pure logic testing!

---

### Test the DataService

```typescript
import { DataService } from '../services/dataService';

describe('DataService', () => {
  let service: DataService;

  beforeEach(() => {
    service = new DataService();
  });

  test('extracts card data correctly', () => {
    const mockDataView = {
      categorical: {
        categories: [{ values: ['A', 'B'] }],
        values: [{ values: [0.5, 1.2] }]
      }
    };

    const cards = service.extractCardData(mockDataView);
    expect(cards.length).toBe(2);
    expect(cards[0].category).toBe('A');
    expect(cards[0].value).toBe(0.5);
  });

  test('throws error on invalid data', () => {
    expect(() => service.extractCardData(null)).toThrow();
  });
});
```

---

## When to Apply Each Pattern

### Pattern 1: Separation of Concerns
**Use when:**
- Visual has 300+ lines of code
- Multiple distinct responsibilities
- Want to test individual pieces

**Example:** Traffic Light, Gauge, Waterfall charts

---

### Pattern 2: MVC
**Use when:**
- Complex data transformations
- Multiple rendering modes
- Need to support different views

**Example:** Flexible matrix, dynamic cross-tab, multi-view dashboard

---

### Pattern 3: Service Layer
**Use when:**
- Reusing logic across visuals
- Want plugin-able architecture
- Need to swap implementations (e.g., mock vs real)

**Example:** API integration, shared calculations, theming service

---

## Adding New Features with Clean Architecture

Let's say you want to add **hover tooltips** showing extra info.

### With messy code (hard):
- Find where cards are created
- Add event listener
- Modify the card HTML
- Update CSS
- Risk breaking existing code

### With clean architecture (easy):

**Step 1:** Create a TooltipService

```typescript
export class TooltipService {
  public showTooltip(element: HTMLElement, data: CardData): void {
    // Tooltip logic
  }
  
  public hideTooltip(element: HTMLElement): void {
    // Hide logic
  }
}
```

**Step 2:** Update Visual constructor

```typescript
this.tooltipService = new TooltipService();
```

**Step 3:** Update RenderService to attach tooltip handlers

```typescript
card.addEventListener('mouseenter', () => {
  this.tooltipService.showTooltip(card, data);
});
```

**Done!** No changes to color logic, data extraction, or styling.

---

## File Organization Best Practices

### Good structure:
```
src/
├── visual.ts                (Main class)
├── services/                (Reusable logic)
│   ├── dataService.ts
│   ├── colorService.ts
│   ├── renderService.ts
│   └── eventService.ts
├── models/                  (Data structures)
│   ├── CardData.ts
│   └── TrafficLightConfig.ts
├── utils/                   (Helper functions)
│   ├── validators.ts
│   └── formatters.ts
├── visual.less              (Styles)
├── capabilities.json
└── settings.ts
```

### Bad structure:
```
src/
├── visual.ts                (1000+ lines of everything!)
├── helpers.ts               (Misc stuff)
└── visual.less
```

---

## Migration Path: From Simple to Organized

If you have an existing visual and want to refactor:

**Phase 1: Extract color logic**
- Create `colorService.ts`
- Move color logic there
- Update visual.ts to use it
- Test thoroughly

**Phase 2: Extract data extraction**
- Create `dataService.ts`
- Move data extraction there
- Update visual.ts

**Phase 3: Extract rendering**
- Create `renderService.ts`
- Move DOM creation there
- Update visual.ts

**Phase 4: Add tests**
- Test each service independently
- Update visual tests

**Phase 5: Add features**
- Much easier now with clean architecture!

---

## Common Mistakes to Avoid

### ❌ Don't create a "Util" class with 10 unrelated methods

```typescript
// Bad
class Utils {
  validateCard() { ... }
  formatDate() { ... }
  getColor() { ... }
  parseJSON() { ... }
}
```

### ✅ Do create focused, named services

```typescript
// Good
class CardValidator { ... }
class DateFormatter { ... }
class ColorService { ... }
class JSONParser { ... }
```

---

### ❌ Don't make services depend on the Visual class

```typescript
// Bad
class DataService {
  constructor(private visual: Visual) { }
  process() {
    this.visual.update();  // Circular dependency!
  }
}
```

### ✅ Do make services independent

```typescript
// Good
class DataService {
  process(): CardData[] {
    return cards;
  }
}

// Visual uses the result
const cards = this.dataService.process();
```

---

## Performance Considerations

With organized code, you can:

1. **Optimize at the service level**
   - Cache expensive calculations in ColorService
   - Batch data processing in DataService
   - Virtual rendering in RenderService (for 10k+ items)

2. **Avoid recreating services**
   ```typescript
   // Good: Create once in constructor
   constructor() {
     this.colorService = new ColorService();
   }

   // Bad: Create every time
   update() {
     const colorService = new ColorService();  // Wasteful!
   }
   ```

3. **Lazy load complex services**
   ```typescript
   if (options.dataViews[0].categorical.values.length > 1000) {
     this.renderService = new VirtualScrollRenderService();
   } else {
     this.renderService = new SimpleRenderService();
   }
   ```

---

## Resources

- **Microsoft samples with good architecture:** [Power BI Visuals Samples](https://github.com/microsoft/PowerBI-visuals)
  - Look at "sampleBarChart" or "samplesLanguageSelector" for patterns
  
- **Design Patterns (book):** Gang of Four patterns apply here
  
- **Clean Code (book):** Robert C. Martin - discusses organization and testing

---

## Next Steps

1. **Refactor your traffic light visual** using this pattern (optional exercise)
2. **Apply to your next visual** from the start
3. **Build a reusable library** of services (ColorService, DataService, etc.) for your org
4. **Add unit tests** using Jest (see Lesson 08 when available)

---

## Summary

**Why organize code:**
- ✅ Easier to read and understand
- ✅ Easier to test
- ✅ Easier to maintain
- ✅ Easier to extend with features
- ✅ Easier to reuse

**Key patterns:**
1. **Separation of Concerns** - Each class does one thing
2. **Model-View-Controller** - Separate data, display, logic
3. **Service Layer** - Reusable, focused services

**Real example:** Refactored traffic light visual shows this in action

The extra effort upfront pays dividends when you add features, fix bugs, or build your next visual.

Happy refactoring! 🚀
