# Framer Developer Skills

This repository is a documented skill library for agents that build, repair, and audit production Framer code components. Each skill lives in its own folder under `skills/` and uses a `SKILL.md` entrypoint.

Use this repository when the task involves Framer code components, property controls, overrides, shared state, runtime safety, routing, forms, CMS extraction, Three.js, or WebGL.

## Repository Layout

```text
Framer-Developer-Skills/
├── README.md
└── skills/
    ├── framer-accessibility/
    │   └── SKILL.md
    ├── framer-animation-state-machine/
    │   └── SKILL.md
    ├── framer-asset-sharing/
    │   └── SKILL.md
    ├── framer-auto-sizing/
    │   └── SKILL.md
    ├── framer-cms-extraction/
    │   └── SKILL.md
    ├── framer-code-override/
    │   └── SKILL.md
    ├── framer-components-reference/
    │   └── SKILL.md
    ├── framer-current-location/
    │   └── SKILL.md
    ├── framer-developer/
    │   └── SKILL.md
    ├── framer-event-bridge/
    │   └── SKILL.md
    ├── framer-fetch/
    │   └── SKILL.md
    ├── framer-form-values/
    │   └── SKILL.md
    ├── framer-property-controls/
    │   └── SKILL.md
    ├── framer-runtime-guards/
    │   └── SKILL.md
    ├── framer-state-orchestration/
    │   └── SKILL.md
    ├── framer-store/
    │   └── SKILL.md
    ├── framer-switch-variant/
    │   └── SKILL.md
    ├── framer-threejs/
    │   └── SKILL.md
    └── framer-webgl/
        └── SKILL.md
```

## Skill Index

| Skill | Folder path | Entrypoint | Use when |
| --- | --- | --- | --- |
| Framer Accessibility | [`skills/framer-accessibility/`](./skills/framer-accessibility/) | [`SKILL.md`](./skills/framer-accessibility/SKILL.md) | Auditing or improving ARIA, keyboard behavior, interaction safety, and shared-state accessibility. |
| Framer Animation State Machine | [`skills/framer-animation-state-machine/`](./skills/framer-animation-state-machine/) | [`SKILL.md`](./skills/framer-animation-state-machine/SKILL.md) | Building multi-stage animation lifecycles, sequencing, and explicit visual states. |
| Framer Asset Sharing | [`skills/framer-asset-sharing/`](./skills/framer-asset-sharing/) | [`SKILL.md`](./skills/framer-asset-sharing/SKILL.md) | Sharing Framer assets, preventing unlinking, and splitting large components into modular helper files. |
| Framer Auto Sizing | [`skills/framer-auto-sizing/`](./skills/framer-auto-sizing/) | [`SKILL.md`](./skills/framer-auto-sizing/SKILL.md) | Choosing layout annotations, intrinsic sizes, fixed sizing, and responsive component behavior. |
| Framer CMS Extraction | [`skills/framer-cms-extraction/`](./skills/framer-cms-extraction/) | [`SKILL.md`](./skills/framer-cms-extraction/SKILL.md) | Reading/exporting CMS data through the official Server API, with published search-index extraction only as a legacy fallback. |
| Framer Code Override | [`skills/framer-code-override/`](./skills/framer-code-override/) | [`SKILL.md`](./skills/framer-code-override/SKILL.md) | Writing overrides, HOCs, refs, MutationObservers, and DOM synchronization logic. |
| Framer Components Reference | [`skills/framer-components-reference/`](./skills/framer-components-reference/) | [`SKILL.md`](./skills/framer-components-reference/SKILL.md) | Looking up core Framer APIs such as `RenderTarget`, localization, and `addPropertyControls`. |
| Framer Current Location | [`skills/framer-current-location/`](./skills/framer-current-location/) | [`SKILL.md`](./skills/framer-current-location/SKILL.md) | Reading current routes, using Framer router state, or syncing behavior to page changes. |
| Framer Developer | [`skills/framer-developer/`](./skills/framer-developer/) | [`SKILL.md`](./skills/framer-developer/SKILL.md) | Solving advanced architecture, runtime optimization, design/canvas/live detection, and modularization issues. |
| Framer Event Bridge | [`skills/framer-event-bridge/`](./skills/framer-event-bridge/) | [`SKILL.md`](./skills/framer-event-bridge/SKILL.md) | Bridging low-level events such as raycasting, pointer events, resize events, or engine events into Framer props. |
| Framer Fetch | [`skills/framer-fetch/`](./skills/framer-fetch/) | [`SKILL.md`](./skills/framer-fetch/SKILL.md) | Connecting Framer to dynamic APIs, JSON data, or custom backends. |
| Framer Form Values | [`skills/framer-form-values/`](./skills/framer-form-values/) | [`SKILL.md`](./skills/framer-form-values/SKILL.md) | Reading and updating Framer/Typer form values through code. |
| Framer Property Controls | [`skills/framer-property-controls/`](./skills/framer-property-controls/) | [`SKILL.md`](./skills/framer-property-controls/SKILL.md) | Designing Framer property panels, migrations, object controls, enum controls, fonts, colors, and hidden logic. |
| Framer Runtime Guards | [`skills/framer-runtime-guards/`](./skills/framer-runtime-guards/) | [`SKILL.md`](./skills/framer-runtime-guards/SKILL.md) | Preventing memory leaks, stale async work, race conditions, dangling listeners, and unsafe runtime effects. |
| Framer State Orchestration | [`skills/framer-state-orchestration/`](./skills/framer-state-orchestration/) | [`SKILL.md`](./skills/framer-state-orchestration/SKILL.md) | Sharing state between components that are not in the same React tree. |
| Framer Store | [`skills/framer-store/`](./skills/framer-store/) | [`SKILL.md`](./skills/framer-store/SKILL.md) | Using Framer shared stores from overrides or code components. |
| Framer Switch Variant | [`skills/framer-switch-variant/`](./skills/framer-switch-variant/) | [`SKILL.md`](./skills/framer-switch-variant/SKILL.md) | Syncing Framer component variants across triggers, controls, and shared state. |
| Framer Three.js | [`skills/framer-threejs/`](./skills/framer-threejs/) | [`SKILL.md`](./skills/framer-threejs/SKILL.md) | Building Three.js systems in Framer with lifecycle safety, resource disposal, slots, and scene architecture. |
| Framer WebGL | [`skills/framer-webgl/`](./skills/framer-webgl/) | [`SKILL.md`](./skills/framer-webgl/SKILL.md) | Managing shared WebGL renderers, GPU context limits, scissor rendering, and low-level render loop performance. |

## Recommended Skill Routing

Use these entrypoints first when the task is ambiguous:

- General Framer code component architecture: [`skills/framer-developer/SKILL.md`](./skills/framer-developer/SKILL.md)
- Property panel or component control changes: [`skills/framer-property-controls/SKILL.md`](./skills/framer-property-controls/SKILL.md)
- Code overrides: [`skills/framer-code-override/SKILL.md`](./skills/framer-code-override/SKILL.md)
- CMS export or inspection: [`skills/framer-cms-extraction/SKILL.md`](./skills/framer-cms-extraction/SKILL.md)
- Runtime cleanup or event safety: [`skills/framer-runtime-guards/SKILL.md`](./skills/framer-runtime-guards/SKILL.md)
- Cross-component state: [`skills/framer-state-orchestration/SKILL.md`](./skills/framer-state-orchestration/SKILL.md)
- Three.js or WebGL runtime work: [`skills/framer-threejs/SKILL.md`](./skills/framer-threejs/SKILL.md) and [`skills/framer-webgl/SKILL.md`](./skills/framer-webgl/SKILL.md)

## Maintenance Rules

- Keep each skill in `skills/<skill-name>/SKILL.md`.
- Add a new row to the Skill Index whenever a new skill folder is added.
- Link to both the folder path and the `SKILL.md` entrypoint.
- Do not add generic folder names that do not exist in `skills/`.
- Keep skill names aligned with the `name:` field inside each `SKILL.md` frontmatter.
