# Bounce Animation Parent Overflow Clipping Fix

## 1. What does this do?
This submission documents and resolves a common UI/layout issue where the `.ease-bounce` utility class gets clipped at the parent container's boundary when `overflow: hidden` (or `overflow: auto / scroll`) is enabled. 

It provides:
- A reproduction of the clipping bug.
- A breakdown of the browser compositing behavior regarding overflow boundaries.
- **Two pure CSS workarounds** (Clearance Padding & Margin-Padding Boundary Extension) that web developers can use to ensure animations render fully without altering layout positions.

---

## 2. Root Cause Analysis
Although EaseMotion CSS animations utilize GPU-accelerated composited transforms (`transform: translate3d(...)` / `translateY(...)`) for optimal performance, the CSS standard requires browser engines to clip any content that extends outside the bounding box of a container marked with `overflow: hidden`.

This means that if a child element bounces upward by `20px` (or more) and sits close to the container's top boundary, it will visually disappear or look cropped at the peak of the animation.

---

## 3. How to Resolve the Clipping Constraint

### Solution A: Clearance Padding (Recommended for Grid/Flow Layouts)
Add inner top padding to the parent container corresponding to the peak bounce translation distance. This keeps the child element within the parent BFC (Block Formatting Context) drawing boundary.

```html
<!-- HTML Structure -->
<div class="card parent-with-padding">
  <div class="ease-bounce">Bouncing Box</div>
</div>
```

```css
/* CSS Styling */
.parent-with-padding {
  overflow: hidden;
  padding-top: 35px; /* Adjust based on bounce height */
}
```

### Solution B: Negative Margin-Padding Offset (Best for Tight/Fixed Layouts)
If adding internal padding isn't acceptable because it shifts other elements down, use a negative `margin-top` matched with positive `padding-top` on the overflow container. This shifts the boundary box of the parent container upwards while keeping layout calculations identical.

```html
<div class="card parent-with-offset-boundary">
  <div class="ease-bounce">Bouncing Box</div>
</div>
```

```css
.parent-with-offset-boundary {
  overflow: hidden;
  
  /* Expand the parent BFC boundary upwards by 35px */
  margin-top: -35px;
  padding-top: 35px;
}
```

---

## 4. Why this fits EaseMotion CSS
EaseMotion CSS provides clean utility-first animation primitives. Since utilities like `.ease-bounce` or `.ease-float` rely on parent layouts, documenting layout limits and showing how to solve them natively avoids developer friction and improves overall reliability across production applications.
