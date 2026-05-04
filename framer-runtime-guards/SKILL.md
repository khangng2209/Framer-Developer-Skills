---
name: framer-runtime-guards
description: Cleanup Patterns, Memory Safety, and Race Condition Prevention in Framer
---

# Framer Runtime Guards

This skill covers the critical "safety engineering" required for components that handle asynchronous events, long-running loops, or global listeners.

## 1. Atomic Cleanup (The 1:1 Rule)
Every subscription or side effect created during mount MUST be destroyed during unmount.

### Listener Guard Pattern
```javascript
useEffect(() => {
    const handle = () => { ... }
    window.addEventListener("scroll", handle)
    
    // THE GUARD: Return the destruction logic immediately
    return () => {
        window.removeEventListener("scroll", handle)
    }
}, [])
```

## 2. Race Condition Protection
Asynchronous callbacks (from `fetch` or `setTimeout`) can fire after a component has unmounted or after a newer interaction has started.

### The "Holder" Revision Pattern
```javascript
const interactionRevision = useRef(0)

async function startAction() {
    const currentRev = ++interactionRevision.current
    const data = await heavyAsyncOperation()
    
    // GUARD: Check if this result is still relevant
    if (currentRev !== interactionRevision.current) return
    
    applyData(data)
}
```

## 3. Interaction Debouncing & Throttling
Prevent "event storms" where multiple rapid clicks or simultaneous touch events cause logic collisions.

### Synthetic Click Guard
```javascript
const lastInteractionAt = useRef(0)

const handleClick = () => {
    const now = Date.now()
    if (now - lastInteractionAt.current < 250) return // Block rapid fire
    lastInteractionAt.current = now
    // logic
}
```

## 4. Practical: Safe Global Bridges
When using global bridges (like `__modelLoadingState`), ensure you check for existence and handle the case where the bridge provider might not be mounted yet.
- **Retry Polling**: Using `setInterval` with a max attempt cap to find the global bridge.
- **Orphan Cleanup**: Ensuring that if your component unmounts while waiting for a bridge, all timers are cleared.

## 5. Engineering Checklist
- [ ] **No Memory Leaks**: Verify `Set` and `Map` objects are cleared.
- [ ] **Safe Callbacks**: Always check if a `ref.current` or `node` still exists before calling methods on it in an async callback.
- [ ] **Event Capture vs Bubble**: Use `capture: true` for guards that need to intercept events before they hit children.
