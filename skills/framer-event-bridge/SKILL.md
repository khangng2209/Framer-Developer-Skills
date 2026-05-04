---
name: framer-event-bridge
description: Bridging System-Level Events (Raycasting, Pointer) to High-Level Framer Props
---

# Framer Event Bridge

This skill covers the engineering required to translate low-level engine events (like WebGL Raycasting) into actionable Framer/React props.

## 1. The Raycast-to-Prop Architecture
In 3D or custom WebGL runtimes, the browser only sees a single `<canvas>`. You must manually bridge clicks to internal objects.

### High-Parity Hit Detection
```javascript
function onPointerDown(event) {
    const rect = canvas.getBoundingClientRect()
    // Normalize coordinates to (-1 to +1)
    const x = ((event.clientX - rect.left) / rect.width) * 2 - 1
    const y = -((event.clientY - rect.top) / rect.height) * 2 + 1
    
    raycaster.setFromCamera({ x, y }, camera)
    const hits = raycaster.intersectObjects(scene.children, true)
    
    if (hits.length > 0) {
        const topHit = hits[0]
        // Emit to Framer property control
        if (props.onMeshClick) {
            props.onMeshClick({ 
                name: topHit.object.name, 
                point: topHit.point 
            })
        }
    }
}
```

## 2. Pointer Proxying & Hover Guards
Ensure interactive elements only respond when the user is *actually* over the content.

### The Hover Guard Logic
```javascript
let isCurrentlyOver = false
function checkHover() {
    const hit = performRaycast()
    if (hit && !isCurrentlyOver) {
        isCurrentlyOver = true
        props.onMouseEnter?.()
    } else if (!hit && isCurrentlyOver) {
        isCurrentlyOver = false
        props.onMouseLeave?.()
    }
}
```

## 3. Practical: Custom Cursor & Mesh Interaction
- **Coordinate Mapping**: Translating global `clientX/Y` to local `canvasX/Y` considering scroll and zoom.
- **Event Forwarding**: Converting a 3D Mesh name (e.g., "Main_Button") into a Framer interaction trigger.

## 4. Engineering Checklist
- [ ] **Canvas Offset**: Always account for `getBoundingClientRect()` to handle responsive layouts.
- [ ] **Debounced Raycasting**: Only raycast on `pointermove` at a maximum frequency of 60Hz.
- [ ] **Interaction Z-Index**: Respect internal 3D scene depth when multiple objects overlap.
