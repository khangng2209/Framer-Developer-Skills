---
name: framer-developer
description: Advanced Framer Architecture, Hacks, and Runtime Optimization
---

# Framer Advanced Developer Skill (Production-Grade)

Use this skill when building or refactoring complex Framer components that require advanced WebGL/Three.js runtime integration or specialized architecture.

## 1. Design-to-Code: Figma Variant Planning
Architecture starts in Figma. Ensure the component structure supports predictable overrides.

- **Outer Instance Ownership:** Always place logic and variant states (Open/Closed, Active/Inactive) on the **outermost** component instance. Overriding inner layers leads to brittle code.
- **Semantic Naming:** Use stable, lowercase-safe names like `closed`, `open`, `active`, `default`. Avoid spaces or special characters in variant names.
- **Handoff:** Document exact variant names so implementation code can target them without trial-and-error.

## 2. Official Framer Development Patterns
Adhere to these official APIs for environment stability.

- **`RenderTarget.current()`**:
    - **`RenderTarget.canvas`**: The Design Canvas (Editor). Disable heavy renderers here.
    - **`RenderTarget.preview`**: Preview or Live. Enable the Bridge/Root here.
- **`addPropertyControls`**: Keep property keys stable. Use `hidden` callback functions to dynamically clean up the UI based on other selected properties.

## 3. Unofficial Hacks & Custom Architecture
Specialized techniques to bypass Framer limitations.

- **The "Blank Tail" Clipper Hack**:
    - **Issue:** Absolute-translated canvases expand the mobile document height.
    - **Fix:** Wrap the canvas in a `data-import-3d-root-canvas-clipper` DIV. Use `overflow: hidden` and `contain: paint`. Sync its height with the document scroll height.
- **The "Ghost Renderer" Suppression**:
    - **Hack:** Force `return` in heavy initialization blocks if `RenderTarget.current() === RenderTarget.canvas`. This ensures the Shared Renderer is inactive in the editor, allowing Slots to take over in Standalone mode so designers can see and edit their models.
- **Global Bridge Debugging**:
    - **Hack:** Expose `window.__IMPORT_3D_RENDERER_DEBUG__.dump()` to inspect registered slots, active owners, and DOM rects directly in the browser console.

## 4. QA Checklist for High-Parity Components
- [ ] **UI Sync:** Does switching a variant on Instance A update Instance B?
- [ ] **Environment Isolation:** Is there exactly one `<canvas>` in Preview?
- [ ] **Mobile Stability:** Is the page bottom clean (no blank space)?
- [ ] **Memory Management:** Are all geometries, materials, and skeletons disposed on unmount?
- [ ] **Interaction Parity:** Do hover/click effects work the same in Standalone vs. Bridge mode?
