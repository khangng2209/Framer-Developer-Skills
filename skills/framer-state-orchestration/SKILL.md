---
name: framer-state-orchestration
description: Advanced Patterns for Inter-Component Communication and Global State in Framer
---

# Framer State Orchestration

This skill covers the engineering of shared state between decoupled components (e.g., a Splash screen responding to a 3D model's loading progress).

## 1. The Singleton Store Pattern
For components that aren't in the same React tree, use a global singleton object.

```javascript
const modelLoadingState = {
    progress: 0,
    listeners: new Set(),
    setProgress(val) {
        if (val > this.progress) { // Monotonic safeguard
            this.progress = val
            this.notify()
        }
    },
    subscribe(cb) {
        this.listeners.add(cb)
        return () => this.listeners.delete(cb)
    },
    notify() { this.listeners.forEach(cb => cb()) }
}
// Expose for cross-module access
window.__modelLoadingState = modelLoadingState
```

## 2. Subscription Strategies

### React Hook Subscription
```javascript
function useModelProgress() {
    const [progress, setProgress] = useState(0)
    useEffect(() => {
        const state = window.__modelLoadingState
        if (!state) return
        return state.subscribe(() => setProgress(state.progress))
    }, [])
    return progress
}
```

### Module Loading Polling
When one component depends on a state created by another component that might load later, use a polling mechanism.
```javascript
const timer = setInterval(() => {
    if (window.__modelLoadingState) {
        clearInterval(timer)
        // start logic
    }
}, 100)
```

## 3. Engineering Best Practices

- **Monotonic Progress**: Progress bars should never jitter or jump backwards. Always use `Math.max(current, next)`.
- **Smooth Smoothing (Lerp)**: Don't display raw progress immediately. Use a `requestAnimationFrame` loop to "chase" the target value for a premium feel.
```javascript
const factor = 1 - Math.pow(0.85, deltaTime / 16)
currentProgress += (target - currentProgress) * factor
```
- **Two-Phase Waiting**: Wait for loading to *start* (with a short timeout) and then wait for it to *complete* (with a long safety timeout).
- **Session Persistence**: Use `sessionStorage` to coordinate if a shared sequence (like an intro) has already played.

## 4. Real-World Example: Splash & Countdown
- **Component A (Splash)**: Blocks the UI, triggers `model.load()`, and listens to `modelLoadingState`.
- **Component B (Countdown)**: Displays the percentage from `modelLoadingState` with a smooth easing animation.
- **Result**: Even though they are separate components on the Framer canvas, they behave as a single integrated system.
