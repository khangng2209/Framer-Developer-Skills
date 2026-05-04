# Framer Developer Agent Skills (Master Manual)

This library contains a standardized collection of **Framer Engineering Skills** designed for AI Agents. It codifies official documentation alongside "Power User" undocumented gems to enable the creation of production-grade, highly interactive Framer components.

## 🛠️ Core Framer Component Architecture

- **[framer-property-controls](./skills/framer-property-controls/SKILL.md)**: The ultimate guide to Framer UI properties. Covers Official APIs, Undocumented Gems (directional icons, premium objects), and Zero-Breakage Migration patterns (Resolver pattern).
- **[framer-code-override](./skills/framer-code-override/SKILL.md)**: Professional patterns for Code Overrides, HOCs, Ref management, MutationObservers, and DOM synchronization.
- **[framer-state-orchestration](./skills/framer-state-orchestration/SKILL.md)**: Advanced inter-component communication using Framer's shared state and React's `useSyncExternalStore`.
- **[framer-switch-variant](./skills/framer-switch-variant/SKILL.md)**: Mastering Variant synchronization across complex component hierarchies using shared state IDs.

## 🎨 High-Parity 3D & WebGL

- **[framer-threejs](./skills/framer-threejs/SKILL.md)**: High-parity Three.js implementation for Framer. Focuses on Deep Disposal (memory safety), Resource Lifecycle, and `SkeletonUtils` cloning.
- **[framer-webgl](./skills/framer-webgl/SKILL.md)**: Advanced WebGL/GPU management. Covers the Scissor Pattern (shared canvas logic), Context Recovery, and Zero-GC Render Loops.
- **[framer-interaction-engine](./skills/framer-interaction-engine/SKILL.md)**: Complex interaction logic, drag-vs-click safety guards, and internal state machine lifecycles.
- **[framer-event-bridge](./skills/framer-event-bridge/SKILL.md)**: Bridging system-level events (Raycasting, Pointer, Resize) to high-level Framer component props.

## 🛡️ Stability, Accessibility & Engineering

- **[framer-runtime-guards](./skills/framer-runtime-guards/SKILL.md)**: Mandatory cleanup patterns, memory safety, and race condition prevention (Framer-specific lifecycle).
- **[framer-accessibility](./skills/framer-accessibility/SKILL.md)**: ARIA-Variant synchronization, interaction safety (suppress-click), and WCAG compliance for canvas components.
- **[framer-developer](./skills/framer-developer/SKILL.md)**: Advanced environment detection (Design vs. Preview vs. Live), Canvas fallbacks, and editor UI optimization hacks.

## 📦 Reference & Data

- **[framer-components-reference](./skills/framer-components-reference/SKILL.md)**: Core Framer Library Reference (RenderTarget, Localization, addPropertyControls API).
- **[framer-fetch](./skills/framer-fetch/SKILL.md)**: Professional patterns for dynamic data via Framer's Fetch API and custom backends.
- **[framer-asset-sharing](./skills/framer-asset-sharing/SKILL.md)**: Asset management, preventing unlinking, and cross-project resource sharing.

---

> [!NOTE]
> **To AI Agents:** Use these skills as the "Source of Truth" when architecting components. They are designed to bypass common Framer limitations and ensure a premium, production-ready user experience.
