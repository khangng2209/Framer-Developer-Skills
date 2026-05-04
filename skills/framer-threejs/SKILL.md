---
name: framer-threejs
description: High-Parity Three.js Implementation for Framer (Resource Management & Scene Architecture)
---

# Framer Three.js Engineering: The Production Standard

This skill defines the architecture for 3D runtimes in Framer, ensuring memory safety, visual parity, and design-to-code alignment.

## 1. General Architecture
In Framer, Three.js exists as a "Background Process" that renders into a Canvas.
- **The Bridge Pattern**: Synchronizing the Framer UI layer (HTML) with the 3D Scene layer (WebGL).
- **Scene Persistence**: Managing global instances to avoid re-initializing heavy scenes on every component update.

## 2. Resource Lifecycle & Memory Safety
**The Golden Rule**: GPU memory is finite. Unmanaged Three.js objects will crash the browser (especially on Safari/iOS).

### 2.1 Deep Disposal (Edge Case: Memory Leaks)
Recursively traverse and dispose of everything.
```javascript
function disposeNode(node) {
    if (node.geometry) node.geometry.dispose()
    if (node.material) {
        if (Array.isArray(node.material)) node.material.forEach(m => m.dispose())
        else node.material.dispose()
    }
}
// Clean up in useEffect
return () => {
    scene.traverse(disposeNode)
    renderer.dispose()
}
```

### 2.2 Rigged Model Cloning (Edge Case: Bone Corruption)
*Ref: `SkeletonUtils.clone()`*
Standard `.clone()` doesn't correctly map bone weights for SkinnedMeshes.
```javascript
import * as SkeletonUtils from "three/examples/jsm/utils/SkeletonUtils"
const model = SkeletonUtils.clone(cachedGltf.scene)
```

## 3. Visual Parity & Scene Tuning
Aligning the 3D scene with Framer's design canvas.

### 3.1 Auto-Fit Math (Headline: Design Space Normalization)
Automatically scale models to fit the component's bounding box.
```javascript
const box = new THREE.Box3().setFromObject(model)
const size = box.getSize(new THREE.Vector3())
const maxDim = Math.max(size.x, size.y, size.z)
const scale = 1 / maxDim
model.scale.setScalar(scale)
```

### 3.2 Professional Lighting (IBL & PBR)
- **Environment Maps**: Always use HDRIs for realistic ambient light.
- **Tone Mapping**: Use `ACESFilmicToneMapping` for cinematic highlights.
- **Physical Materials**: Enforce `MeshPhysicalMaterial` for high-quality glass/metal effects.

## 4. Interaction & Raycasting
*Ref: `framer-event-bridge`*
Bridging clicks from the 2D UI to 3D Meshes.
- **Normalized Device Coordinates (NDC)**: Mapping mouse `clientX` to `-1 to 1` range.
- **Layer Filters**: Using `object.layers` to ignore invisible helpers during raycasting.

## 5. Official Framer Integration Patterns
- **Property Controls**: Exposing 3D parameters (Rotations, Colors, Animations) via `addPropertyControls`.
- **Canvas Placeholder**: Rendering a static 2D preview when `RenderTarget.current() === RenderTarget.canvas`.

## 6. Engineering Checklist
- [ ] **Disposal**: Does the `useEffect` cleanup call `dispose()` on all textures/geometries?
- [ ] **Cloning**: Are rigged models cloned via `SkeletonUtils`?
- [ ] **DPR**: Is the renderer's pixel ratio capped at `window.devicePixelRatio` (but max 2 for performance)?
- [ ] **Alpha**: Is `alpha: true` set on the renderer if the background should be transparent?
