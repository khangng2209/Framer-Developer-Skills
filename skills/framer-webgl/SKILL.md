---
name: framer-webgl
description: Advanced WebGL Management and Optimization for Framer Runtimes
---

# Framer WebGL: Low-Level GPU Performance

This skill covers the optimization and coordination of WebGL contexts within the Framer ecosystem, specifically for multi-canvas sites.

## 1. General Overview
WebGL is a low-level API. In Framer, managing multiple contexts is the biggest challenge (Safari limits sites to ~16 contexts).
- **The Context Hub**: Using a single shared Renderer for multiple "Slot" components.
- **The Draw Loop**: Synchronizing updates with the browser's refresh rate using `requestAnimationFrame`.

## 2. Official Framer Patterns
- **Shared Canvas**: A "Singleton" pattern where one canvas covers the entire screen, and components define "Scissor Regions" to render into.
- **Asset Preloading**: Using `Suspense` or global promises to ensure assets are ready before the WebGL loop starts.

## 3. Engineering Best Practices
- **Draw Call Minimization**: Merging geometries or using `InstancedMesh` for repeated objects.
- **Texture Compression**: Using Basis Universal or KTX2 to reduce GPU memory footprint.
- **Dynamic DPR**: Reducing the `devicePixelRatio` when performance drops below 30FPS.

## 4. Advanced Edge Cases (Battle-Tested)

### 4.1 Edge Case: Safari Context Limit (The Scissor Pattern)
*Ref: `ThreeJS_Runtime.tsx`*
Instead of one canvas per component, render everything into one full-screen canvas using `setScissorTest`.
```javascript
renderer.setScissorTest(true)
renderer.setScissor(rect.left, rect.bottom, rect.width, rect.height)
renderer.setViewport(rect.left, rect.bottom, rect.width, rect.height)
renderer.render(scene, camera)
```

### 4.2 Edge Case: Viewport Visibility (Culling)
If a 3D component is off-screen, **stop rendering it**.
```javascript
const observer = new IntersectionObserver((entries) => {
    isPaused = !entries[0].isIntersecting
}, { threshold: 0.1 })
observer.observe(canvas)
```

### 4.3 Edge Case: GPU Hangs (Error Recovery)
Handle `webglcontextlost` events to prevent the entire Framer site from crashing.
```javascript
canvas.addEventListener("webglcontextlost", (e) => {
    e.preventDefault()
    console.warn("GPU Context Lost. Re-initializing...")
    rebootRenderer()
}, false)
```

## 5. Comprehensive Examples

### The Zero-GC Frame Loop
Ensure the render loop doesn't create new objects (Vectors/Matrices) to avoid Garbage Collection (GC) stutters.
```javascript
const _tempVec = new THREE.Vector3() // Pre-allocate outside loop
function update() {
    _tempVec.set(0, 0, 0) // Re-use
    // logic
}
```

## 6. Implementation Checklist
- [ ] **Context Budget**: Does the site use more than 10 canvases? If so, switch to Bridge/Scissor mode.
- [ ] **Visiblity Culling**: Do off-screen components pause their render loops?
- [ ] **GC Safety**: Is the render loop free of object creation (`new Vector3`)?
- [ ] **Anti-Aliasing**: Is MSAA enabled (but disabled on high-DPI screens for performance)?
