---
name: framer-accessibility
description: Framer-Specific Accessibility Patterns and Shared-State Coordination
---

# Framer Accessibility: Standardizing Interactions

Accessibility in Framer is not just about ARIA tags; it's about making complex, non-native components (Carousels, Tabs, 3D) behave predictably for all users.

## 1. General Concepts
Professional accessibility ensures that custom components:
- **Announce their state** (ARIA roles).
- **Handle gestures safely** (No accidental clicks).
- **Maintain focus and flow** (Keyboard navigation).

## 2. Official Framer Patterns
- **Semantic HTML**: Use the "Layer Name" or `aria-label` property in Framer's UI.
- **Accessibility Panel**: Leverage Framer's built-in accessibility settings where possible.
- **Dynamic Text**: Ensure screen readers are notified of dynamic changes using `aria-live`.

## 3. Engineering Best Practices
- **Gesture Guards**: Always separate "Dragging" from "Clicking".
- **Scroll Management**: Lock the page scroll when high-priority UI (Modals, Splash screens) is active.
- **Pointer Events**: Use `pointer-events: none` on layers that are visually hidden but still exist in the DOM.

## 4. Advanced Edge Cases (Battle-Tested)

### 4.1 Edge Case: Drag-to-Click False Positives (Click Guard)
*Ref: `Carousel.tsx`*
On mobile, the sequence `TouchStart -> Drag -> TouchEnd` often triggers a `click` event on the target element.
```javascript
const suppressUntil = useRef(0)
// On DragEnd (if move > 10px)
suppressUntil.current = Date.now() + 50 

// On the Button/Link inside the carousel
const handleTap = () => {
    if (Date.now() < suppressUntil.current) return // BLOCK
    // execute actual click
}
```

### 4.2 Edge Case: High-Frequency Text Sync (Performance vs ARIA)
*Ref: `Carousel.tsx`, `Loading_splash.tsx`*
Updating React state 60 times a second for a counter (e.g., "1 / 5") is expensive. Bypass React but maintain ARIA.
```javascript
function updateCounter(node, current, total) {
    if (!node) return
    const text = `${current} / ${total}`
    node.textContent = text // Performance: Direct DOM
    node.setAttribute("aria-label", `Page ${current} of ${total}`) // Accessibility: Live sync
}
```

### 4.3 Edge Case: Scroll-Lock Cleanup (Environment Safety)
*Ref: `Loading_splash.tsx`*
Locking the scroll must be reversible and environment-safe.
```javascript
useEffect(() => {
    const original = document.documentElement.style.overflow
    document.documentElement.style.overflow = "hidden"
    return () => {
        document.documentElement.style.overflow = original
    }
}, [])
```

## 5. Comprehensive Examples

### Accessible Tab Role Mapping
Syncing visual variants directly to ARIA selected states.
```javascript
export function withTabAria(props): Override {
    const isActive = props.variant === "Active"
    return {
        role: "tab",
        "aria-selected": isActive,
        tabIndex: isActive ? 0 : -1,
    }
}
```

## 6. Implementation Checklist
- [ ] **Drag Guard**: Do carousels prevent accidental link clicks?
- [ ] **Live Regions**: Are dynamic updates (counters, loading %) announced?
- [ ] **Pointer Safety**: Are hidden layers using `pointer-events: none`?
- [ ] **Tab Order**: Can the component be navigated via Keyboard (`Tab` key)?
- [ ] **Scroll Locking**: Does the page scroll correctly unlock after a Splash screen?
