---
name: framer-switch-variant
description: Syncing Framer Variants via Code Overrides and Shared State
---

# Framer Switch Variant: Global State Orchestration

This skill defines how to coordinate multiple components across the Framer canvas using a central source of truth. It is the logic pillar of the **Trinity of Advanced Components**.

## 1. General Concepts
Variant switching is the process of telling a Framer Component to change its visual state (e.g., from "Idle" to "Active") based on external logic or a shared store.
- **The Trigger**: An interaction (Click, Scroll, Hover) in one override.
- **The Store**: A central object that holds the current state.
- **The Listener**: An override on a different component that subscribes to the store and switches its `variant` prop.

## 2. Official Framer Patterns
- **`useStore` (Legacy)**: Used for simple cross-component variables.
- **Props Delegation**: Passing a `variant` string directly to a component wrapped in a Code Override.
- **External State Sync**: For high-performance apps, use `useSyncExternalStore` (React 18 standard).

## 3. Engineering Best Practices
- **Hierarchical Resolution**: Don't hardcode IDs. Use DOM traversal to find the nearest "Group" or "Section" ID.
- **State Normalization**: Always sanitize strings (e.g., `.trim().toLowerCase()`) before comparing them to variant names.
- **Debounced Updates**: If a state changes rapidly (like scroll progress), debounce the variant switch to prevent flickering.

## 4. Advanced Edge Cases (Battle-Tested)

### 4.1 Edge Case: Hierarchical ID Lookup (Intelligent Routing)
*Ref: `Calc.tsx`, `Carousel.tsx`*
When multiple carousels or tab systems exist on one page, ensure an override only responds to its own "Group".
```javascript
function resolveGroup(node) {
    let current = node
    while (current) {
        // Look for data attributes set by the designer or parent overrides
        const id = current.getAttribute("data-section-id") || current.id
        if (id) return id
        current = current.parentElement
    }
    return "default"
}
```

### 4.2 Edge Case: Monotonic Progress Jitter
*Ref: `Loading_splash.tsx`*
For progress-based variant switching (e.g., 0% to 100%), ensure the state never moves backwards, even if asset loading reports inconsistent values.
```javascript
setProgress(val) {
    // Only update if the new value is greater (Monotonic)
    if (val > this.progress) {
        this.progress = val
        this.notify()
    }
}
```

### 4.3 Edge Case: Variant String Mismatch
Framer variants are Case-Sensitive.
- **Rule**: Always map your store values to a strictly defined list of variants.
```javascript
const variantMap = {
    active: "Active",
    inactive: "Inactive",
    loading: "Loading"
}
return { variant: variantMap[store.state] || "Default" }
```

## 5. Comprehensive Examples

### The Shared Store Scaffolding
```javascript
const createStore = () => {
    let state = {}
    const listeners = new Set()
    return {
        subscribe: (cb) => { listeners.add(cb); return () => listeners.delete(cb) },
        getSnapshot: () => state,
        set: (key, val) => { state = { ...state, [key]: val }; listeners.forEach(l => l()) }
    }
}
```

## 6. Implementation Checklist
- [ ] Are variant names 100% matching (including Case)?
- [ ] Does the lookup logic handle nested components correctly?
- [ ] Is `useSyncExternalStore` used for high-frequency updates?
- [ ] Is there a fallback for when the store is empty or undefined?
