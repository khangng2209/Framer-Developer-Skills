---
name: framer-code-override
description: Professional Patterns for Framer Code Overrides (HOCs, Refs, and State)
---

# Framer Code Overrides: The Professional Engineering Standard

Code Overrides are the bridge between Framer's visual design canvas and custom React logic. This skill defines the **Trinity of Advanced Components** standard.

## 1. General Architecture & Lifecycle
Overrides are essentially High-Order Components (HOCs) that wrap a designer's layer.
- **Initialization**: Overrides run when the component is about to be rendered.
- **Ref Binding**: The `ref` in an override is the direct access to the DOM node.
- **Runtime detection**: Use `RenderTarget.current()` to differentiate between the Editor (`canvas`) and the live site (`live`).

## 2. Official Framer Patterns
Follow these standards from the official developer documentation:
- **The Data Object**: Use `Data()` for simple shared state between overrides.
- **Override Type**: Always type your functions as `Override` or `ComponentType`.
- **RenderTarget Safety**:
```javascript
import { RenderTarget } from "framer"
if (RenderTarget.current() === RenderTarget.canvas) {
    // Provide a stable experience for the designer
}
```

## 3. Engineering Best Practices
- **Ref Hoisting**: Always use `forwardRef`. If you wrap a component, the ref must reach the underlying DOM element for Framer's layout engine to work.
- **State Hoisting**: Keep state in a central store (like `useSyncExternalStore`) to coordinate multiple overrides.
- **Performance**: Avoid putting heavy logic inside the `return {}` object. Move logic into `useCallback` or `useEffect`.

## 4. Advanced Edge Cases (Battle-Tested)

### 4.1 Edge Case: Dynamic Content Style Reset (MutationObserver)
*Ref: `textweight.tsx`*
Framer's content engine often resets inline styles when text changes. Use an observer to enforce them.
```javascript
export function enforceStyle(props): Override {
    return {
        ref(node) {
            if (!node) return
            const update = () => {
                const p = node.querySelector("p") || node
                p.style.fontWeight = props.fontWeight
            }
            const observer = new MutationObserver(update)
            observer.observe(node, { childList: true, subtree: true, characterData: true })
            update() // Initial call
        }
    }
}
```

### 4.2 Edge Case: Zero-Delay DOM Binding (Callback Refs)
*Ref: `Calc.tsx`, `Tabs.tsx`*
Standard `useRef` + `useEffect` can sometimes trigger one frame late. Use a callback ref for immediate logic.
```javascript
const ref = useCallback((node) => {
    if (!node) return
    // Logic that needs to happen the microtask the node mounts
    const rect = node.getBoundingClientRect()
    setInitialPos(rect.top)
}, [])
```

### 4.3 Edge Case: Drag-Click Collision (Interaction Guard)
*Ref: `Carousel.tsx`*
On touch devices, a "Drag" often triggers a "Click" at the end. Use a ref-based timestamp guard.
```javascript
const suppressUntil = useRef(0)
return {
    onDragEnd: (e, info) => {
        if (Math.abs(info.offset.x) > 10) suppressUntil.current = Date.now() + 50
    },
    onTap: (e) => {
        if (Date.now() < suppressUntil.current) return
        // Actual logic
    }
}
```

## 5. Comprehensive Examples

### A. The "Smart Tab" Indicator
Using `useLayoutEffect` and `ResizeObserver` to perfectly track a layer's position.
```javascript
export function withIndicator(props): Override {
    const [rect, setRect] = useState({ left: 0, width: 0 })
    const ref = useCallback((node) => {
        if (!node) return
        const update = () => setRect(node.getBoundingClientRect())
        const ob = new ResizeObserver(update)
        ob.observe(node)
        update()
    }, [])
    return { ref, animate: { x: rect.left, width: rect.width } }
}
```

## 6. Implementation Checklist
- [ ] Is `forwardRef` used for all stateful overrides?
- [ ] Are all `Observers` and `EventListeners` cleared in a cleanup function?
- [ ] Is the code environment-aware (`RenderTarget`)?
- [ ] Does it handle the "Drag vs Click" mobile edge case?
