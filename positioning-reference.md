# HTML Layout & Positioning Reference

Quick reference for understanding how different elements work when positioning and laying out web pages.

---

## Viewport

**What it is:**
- The browser window itself (the visible area)
- NOT an HTML element - it's the "stage" everything sits on
- The reference point for all measurements

**Units:**
- `100vh` = 100% of viewport height (full window height)
- `100vw` = 100% of viewport width (full window width)
- `50vh` = half the window height
- `50vw` = half the window width

**Important:**
- You CANNOT reposition the viewport
- Everything else positions relative to it
- If content is taller than viewport + `overflow: hidden`, content gets cut off

---

## Canvas

**What it is:**
- An HTML element: `<canvas width="600" height="600"></canvas>`
- A special drawing surface for JavaScript graphics
- For positioning purposes, it's just like a `<div>`

**Key properties:**
- Has fixed pixel dimensions: `width` and `height` attributes
- Takes up space in the layout
- Can be styled with CSS (borders, margins, padding, etc.)
- Positions exactly like any other block element

**Example:**
```html
<canvas id="game-canvas" width="600" height="600"></canvas>
```
```css
#game-canvas {
    border: 4px solid #FF6B35;
    margin: 0 auto;  /* Centers horizontally */
}
```

---

## Button

**What it is:**
- An HTML element: `<button>Click me</button>`
- A regular interactive element

**For positioning:**
- No different from a `<div>` or `<span>`
- Positions normally with CSS
- Can be styled with margin, padding, position, etc.

**Can also be a container:**
```html
<button>
    <span>🎮</span> Play Game
</button>
```

---

## Containers

**What they are:**
- ANY HTML element that holds other elements
- The parent controls layout of its children

**Common containers:**
- `<div>` (most common general container)
- `<body>` (contains entire page)
- `<section>`, `<main>`, `<header>`, `<footer>`
- `<ul>` (contains `<li>` items)
- Even `<button>` can contain text/icons

**Example:**
```html
<div class="game-wrapper">     ← CONTAINER
    <h1>Title</h1>              ← CHILD
    <div class="stats">         ← CHILD (also a container)
        <span>Score: 0</span>   ← GRANDCHILD
    </div>
    <canvas></canvas>           ← CHILD
    <button>Play</button>       ← CHILD
</div>
```

**Container CSS properties:**
```css
.game-wrapper {
    display: flex;              /* Flexbox layout */
    flex-direction: column;     /* Stack children vertically */
    justify-content: center;    /* Center children vertically */
    align-items: center;        /* Center children horizontally */

    max-height: 95vh;          /* Don't exceed 95% of viewport */
    overflow: auto;            /* Scroll if content too tall */

    padding: 20px;             /* Space inside container */
    gap: 10px;                 /* Space between children */
}
```

---

## Common Layout Issues

### Issue: Content cut off at bottom

**Cause:**
```css
body {
    min-height: 100vh;   /* Body CAN grow beyond viewport */
    overflow: hidden;    /* Scrolling disabled */
}
```

**Result:** When content exceeds viewport height, bottom gets cut off

**Symptoms:**
- Can't see controls at bottom
- Game canvas partially hidden
- Content disappears below visible area

### Issue: Content too tall for container

**Cause:**
```css
.game-wrapper {
    max-height: 95vh;    /* Container limited to 95% viewport */
}
/* But children total more than 95vh! */
```

**Solutions:**
1. **Make container scrollable:**
   ```css
   .game-wrapper {
       max-height: 95vh;
       overflow-y: auto;  /* Allow vertical scrolling */
   }
   ```

2. **Reduce child sizes:**
   ```html
   <!-- Instead of 600x600 canvas: -->
   <canvas width="500" height="500"></canvas>
   ```

3. **Use flexible sizing:**
   ```css
   #game-canvas {
       max-height: 60vh;  /* Canvas adapts to viewport */
       height: auto;
   }
   ```

4. **Reduce padding/margins:**
   ```css
   .game-wrapper {
       padding: 10px;  /* Instead of 20px */
   }
   h1 {
       margin: 5px 0;  /* Instead of 10px */
   }
   ```

---

## Positioning Comparison

| Element Type | Positioning Behavior | Can Contain Children? | Example |
|--------------|---------------------|----------------------|---------|
| **Viewport** | Reference point (can't move) | All page content | Browser window |
| **Container** | Like any element | ✅ Yes | `<div>`, `<section>` |
| **Canvas** | Like any block element | ❌ No (drawing surface) | `<canvas>` |
| **Button** | Like any inline-block element | ✅ Yes (text/icons) | `<button>` |
| **Div** | Like any block element | ✅ Yes | `<div>` |

---

## Quick Troubleshooting

**Content cut off at bottom?**
1. Check if total content height > viewport height
2. Check if parent has `overflow: hidden`
3. Check if parent has `max-height` constraint
4. Add up all: canvas height + padding + margins + other elements

**Can't center content?**
1. Is parent a flex container? (`display: flex`)
2. Is `justify-content: center` set on parent?
3. Is parent height constrained? (needs `height: 100vh`, not `min-height`)
4. Are there conflicting `position: absolute` rules?

**Container not constraining children?**
1. Add `max-height` to container
2. Add `overflow: auto` to allow scrolling
3. Or reduce child sizes to fit

---

## Real Example: Snake Game

```html
<body>                          <!-- Container (viewport-sized) -->
    <div class="game-wrapper">  <!-- Container (constrained to 95vh) -->
        <h1>Snake</h1>          <!-- Child (~60px) -->
        <div class="stats">     <!-- Child (~40px) -->
        <canvas height="600">   <!-- Child (600px) -->
        <div class="controls">  <!-- Child (~50px) -->
    </div>
</body>
```

**Total height:** ~750px (60 + 40 + 600 + 50)
**Available space:** 95vh ≈ 680px on a 720px tall window
**Problem:** 750px doesn't fit in 680px!

**Solutions:**
- Reduce canvas to 500x500
- Reduce margins/padding
- Make container scrollable
- Use smaller font sizes
