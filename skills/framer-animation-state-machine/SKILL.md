---
name: framer-animation-state-machine
description: Complex Animation Lifecycles, Sequencing, and State Transitions in Framer
---

# Framer Animation State Machine

This skill covers the engineering of multi-stage animation sequences and state transitions, ensuring visual consistency and logical integrity.

## 1. Multi-Stage Lifecycle Management
Avoid using simple booleans for complex sequences. Use explicit states: `idle`, `starting`, `running`, `finishing`, `completed`.

### The Sequence Orchestrator
```javascript
const sequence = {
    state: "idle",
    async start() {
        this.state = "starting"
        await playIntro()
        
        this.state = "running"
        await playMainLoop()
        
        this.state = "finishing"
        await playOutro()
        
        this.state = "completed"
    }
}
```

## 2. Monotonic & Smooth Transitions
Ensure animations never jitter or jump backwards, especially when tied to asynchronous data (like loading progress).

### Engineering Standard: Smooth Chase (Lerp)
Instead of jumping to a value, "chase" it every frame for a premium feel.
```javascript
// High-performance animation loop (Zero-GC)
function step(time) {
    const deltaTime = time - lastTime
    const diff = targetValue - currentValue
    
    // Smooth chase: move a percentage of the distance each frame
    const factor = 1 - Math.pow(0.85, deltaTime / 16.6)
    currentValue += diff * factor
    
    updateDOM(currentValue)
    requestAnimationFrame(step)
}
```

## 3. Practical: Splash Screen Transitions
Handling the transition from a "Start" variant to an "End" variant with intermediate scaling logic.
- **Trigger**: Page load complete.
- **Action**: Scale up (4x) + Fade out (opacity: 0).
- **State Lock**: Set `sessionStorage` key `hasVisited` ONLY after the `completed` state is reached.

## 4. Engineering Checklist
- [ ] **State Isolation**: Ensure only one transition can run at a time to prevent "logic soup".
- [ ] **Cancellation Path**: Every asynchronous sequence must handle component unmounting gracefully.
- [ ] **Frame Budget**: Keep per-frame calculations under 16ms to maintain 60fps.
