---
name: framer-property-controls
description: Complete Engineering Guide to Framer Property Controls, Official Controls, and Framer UI Hidden Gems
---

# Framer Property Controls

Property Controls are the contract between a Code Component and the Framer editor UI. Treat the control schema as a public API: keep keys stable, keep defaults synchronized, and use resolver functions when changing shape.

Use this skill whenever a task touches `addPropertyControls`, `ControlType`, Framer panel UX, hidden logic, enum tabs, icon controls, object controls, or prop migration.

## Core Rules

- Always import both `addPropertyControls` and `ControlType` from `framer`.
- Always define `defaultProps` for canvas stability, even when each control has `defaultValue`.
- Keep prop keys stable. If a key changes, keep a fallback in the resolver.
- Prefer official modern controls over deprecated controls.
- Do not expose complex nested objects unless the editor UX genuinely benefits.
- Use `hidden(props)` to keep the panel focused, but keep the resolver tolerant of hidden/legacy values.

```tsx
import * as React from "react"
import { addPropertyControls, ControlType } from "framer"

type Props = {
    text?: string
    color?: string
}

export default function MyComponent(props: Props) {
    const { text = "Hello World", color = "#000000" } = props
    return <div style={{ color }}>{text}</div>
}

MyComponent.defaultProps = {
    text: "Hello World",
    color: "#000000",
}

addPropertyControls(MyComponent, {
    text: {
        type: ControlType.String,
        title: "Text",
        defaultValue: "Hello World",
    },
    color: {
        type: ControlType.Color,
        title: "Color",
        defaultValue: "#000000",
    },
})
```

## Official Control Types

### Array

Use `ControlType.Array` for repeatable values. Always set `maxCount` for editor performance.

```tsx
items: {
    type: ControlType.Array,
    title: "Items",
    maxCount: 8,
    control: {
        type: ControlType.Object,
        controls: {
            title: { type: ControlType.String, defaultValue: "Item" },
            image: { type: ControlType.ResponsiveImage },
        },
    },
}
```

Use `Array + ComponentInstance` when a component needs multiple canvas-connected children.

```tsx
children: {
    type: ControlType.Array,
    title: "Children",
    maxCount: 6,
    control: { type: ControlType.ComponentInstance },
}
```

### Boolean

Use for binary on/off settings. Set `enabledTitle` and `disabledTitle` when the labels clarify behavior.

```tsx
enabled: {
    type: ControlType.Boolean,
    title: "Enabled",
    defaultValue: true,
    enabledTitle: "On",
    disabledTitle: "Off",
}
```

### Color

Returns a CSS color string. Use `optional: true` when the absence of a color is meaningful.

```tsx
background: {
    type: ControlType.Color,
    title: "Fill",
    defaultValue: "#ffffff",
    optional: true,
}
```

### ComponentInstance

Use for a single child connection from the canvas. Conventionally name the prop `children` for single-slot components.

```tsx
children: {
    type: ControlType.ComponentInstance,
    title: "Content",
}
```

### Date

Returns an ISO date string. Use `displayTime` when time matters.

```tsx
date: {
    type: ControlType.Date,
    title: "Date",
    displayTime: true,
}
```

### Enum

Use for fixed option sets. `ControlType.SegmentedEnum` is deprecated; use `ControlType.Enum` with `displaySegmentedControl`.

```tsx
variant: {
    type: ControlType.Enum,
    title: "Variant",
    options: ["solid", "soft", "ghost"],
    optionTitles: ["Solid", "Soft", "Ghost"],
    defaultValue: "solid",
    displaySegmentedControl: true,
    segmentedControlDirection: "horizontal",
}
```

### EventHandler

Use to expose interaction hooks in Framer's Interactions panel.

```tsx
onTap: {
    type: ControlType.EventHandler,
}
```

### File

Returns a file URL. Always set `allowedFileTypes`.

```tsx
video: {
    type: ControlType.File,
    title: "Video",
    allowedFileTypes: ["mp4", "mov", "webm"],
}
```

### ResponsiveImage

Use instead of deprecated `ControlType.Image`. The value is an object with `src`, optional `srcSet`, and optional `alt`.

```tsx
image: {
    type: ControlType.ResponsiveImage,
    title: "Image",
}
```

### Number

Use `unit`, `step`, and `displayStepper` deliberately. Avoid unconstrained numeric controls when values feed layout or animation.

```tsx
rotation: {
    type: ControlType.Number,
    title: "Rotate",
    defaultValue: 0,
    min: 0,
    max: 360,
    step: 1,
    unit: "deg",
    displayStepper: true,
}
```

### Object

Use for related settings. Use `optional`, `buttonTitle`, and `icon` to make nested groups feel native.

```tsx
style: {
    type: ControlType.Object,
    title: "Style",
    optional: true,
    buttonTitle: "Add Style",
    icon: "color",
    controls: {
        opacity: {
            type: ControlType.Number,
            min: 0,
            max: 1,
            step: 0.05,
            defaultValue: 1,
        },
        fill: {
            type: ControlType.Color,
            defaultValue: "#ffffff",
        },
    },
}
```

Known object icons: `boolean`, `object`, `color`, `effect`, `interact`.

### String

Use `displayTextArea` for long copy, `obscured` for secrets shown in the editor, and `maxLength` when text can break layout.

```tsx
label: {
    type: ControlType.String,
    title: "Label",
    defaultValue: "Continue",
    placeholder: "Button label",
    maxLength: 48,
}
body: {
    type: ControlType.String,
    title: "Body",
    displayTextArea: true,
}
apiKey: {
    type: ControlType.String,
    title: "API Key",
    obscured: true,
}
```

Do not store real API secrets in public Code Components. `obscured` hides the field in the editor; it does not protect bundled client-side code.

### Transition

Use for Framer Motion transition settings.

```tsx
transition: {
    type: ControlType.Transition,
    defaultValue: { type: "spring", stiffness: 800, damping: 60 },
}
```

### Link

Use for external or internal web links.

```tsx
link: {
    type: ControlType.Link,
    title: "Link",
    defaultValue: "https://www.framer.com",
}
```

### Padding

Use modern `ControlType.Padding` instead of deprecated `FusedNumber` for CSS padding.

```tsx
padding: {
    type: ControlType.Padding,
    title: "Padding",
    defaultValue: "12px 16px",
}
```

The prop is a CSS-compatible string. Use it directly in style objects.

```tsx
<div style={{ padding: props.padding }} />
```

### BorderRadius

Use modern `ControlType.BorderRadius` instead of deprecated `FusedNumber` for radius.

```tsx
borderRadius: {
    type: ControlType.BorderRadius,
    title: "Radius",
    defaultValue: "12px",
}
```

### Border

Use `ControlType.Border` for full CSS border style objects. It can return `borderWidth` or per-side width values.

```tsx
border: {
    type: ControlType.Border,
    title: "Border",
    defaultValue: {
        borderWidth: 1,
        borderStyle: "solid",
        borderColor: "rgba(0, 0, 0, 0.16)",
    },
}
```

### BoxShadow

Use `ControlType.BoxShadow` where available. Keep a fallback when supporting older Framer runtimes.

```tsx
const SHADOW_CONTROL_TYPE = (ControlType as any).BoxShadow || ControlType.Object

shadow: {
    type: SHADOW_CONTROL_TYPE,
    title: "Shadow",
    defaultValue: "0px 1px 2px 0px rgba(0,0,0,0.25)",
}
```

### Gap

Use `ControlType.Gap` for two-dimensional CSS gaps. The value is a CSS-compatible string like `"8px 16px"` where the first value is vertical gap and the second value is horizontal gap.

```tsx
gap: {
    type: ControlType.Gap,
    title: "Gap",
    defaultValue: "8px 16px",
}
```

### Font

Use `ControlType.Font` for typography. Prefer `controls: "extended"` when the component is text-forward; prefer `controls: "basic"` for compact panels.

```tsx
font: {
    type: ControlType.Font,
    title: "Font",
    controls: "extended",
    defaultFontType: "sans-serif",
    defaultFontSize: "16px",
    displayTextAlignment: true,
    displayFontSize: true,
    defaultValue: {
        fontSize: 16,
        fontFamily: "Inter, system-ui, sans-serif",
        fontWeight: 500,
        lineHeight: "1.2em",
        letterSpacing: "0em",
    },
}
```

### Cursor

Use when the component needs a specific hover cursor.

```tsx
cursor: {
    type: ControlType.Cursor,
    title: "Cursor",
    defaultValue: "pointer",
}
```

### TrackingId

Use for analytics-friendly custom event identifiers. Values should be lowercase and hyphen-separated.

```tsx
trackingId: {
    type: ControlType.TrackingId,
    title: "Tracking ID",
}
```

## Hidden Gems And Panel UX Hacks

These are practical Framer panel patterns that agents should apply without waiting for explicit follow-up instructions.

### Icon Tabs / Segmented Toolbar Hack

When a setting represents a compact mode, alignment, direction, action, or view tab, use `ControlType.Enum` with `displaySegmentedControl` and `optionIcons`. This produces a compact icon-tab UI instead of text-heavy dropdowns.

```tsx
align: {
    type: ControlType.Enum,
    title: "Align",
    options: ["left", "center", "right"],
    optionTitles: ["Left", "Center", "Right"],
    optionIcons: ["align-left", "align-center", "align-right"],
    displaySegmentedControl: true,
    defaultValue: "center",
}
```

Use icon tabs for:

- Alignment: `align-left`, `align-center`, `align-right`
- Distribution: `space-between`, `space-around`, `space-evenly` when available
- Direction: arrow icons or orientation icons
- Text style: bold/italic-style mode controls when available
- Tool modes: select, hand, draw, crop, move, rotate, zoom when available
- View tabs: list/grid/columns-style controls when available

Keep `optionTitles` even when icons are present. Titles improve accessibility and reduce ambiguity in the editor.

### Directional Option Icons

For controls whose visual meaning changes with orientation, use directional `optionIcons`.

```tsx
alignment: {
    type: ControlType.Enum,
    title: "Align",
    options: ["start", "center", "end"],
    optionTitles: ["Start", "Center", "End"],
    optionIcons: {
        directions: {
            right: ["align-top", "align-middle", "align-bottom"],
            left: ["align-top", "align-middle", "align-bottom"],
            top: ["align-left", "align-center", "align-right"],
            bottom: ["align-left", "align-center", "align-right"],
        },
    },
    displaySegmentedControl: true,
    defaultValue: "center",
}
```

### Control Grouping Into A Single Style Object

When the panel starts showing many small top-level controls, group visual controls into one `Style` object.

```tsx
appearance: {
    type: ControlType.Object,
    title: "Style",
    controls: {
        padding: {
            type: ControlType.Padding,
            defaultValue: "12px 16px",
        },
        radius: {
            type: ControlType.BorderRadius,
            defaultValue: "10px",
        },
        border: {
            type: ControlType.Border,
            defaultValue: {
                borderWidth: 1,
                borderStyle: "solid",
                borderColor: "rgba(0,0,0,0.12)",
            },
        },
        shadow: {
            type: (ControlType as any).BoxShadow || ControlType.String,
            defaultValue: "0px 1px 2px rgba(0,0,0,0.18)",
        },
    },
}
```

Use this pattern for `Size`, `Padding`, `Radius`, `Border`, `Colors`, `Shadow`, and similar styling groups when the user asks to avoid scattered control objects.

### Description As Documentation

`description` supports Markdown links, emphasis, and line breaks with `\n`. Use it for short guidance, not long docs.

```tsx
source: {
    type: ControlType.String,
    title: "Source",
    description: "Paste a public URL.\n[Docs](https://www.framer.com/developers/)",
}
```

### Optional Controls

Use `optional: true` when users need to intentionally remove a value.

```tsx
accent: {
    type: ControlType.Color,
    title: "Accent",
    optional: true,
}
```

### Deprecated FusedNumber Fallback

`ControlType.FusedNumber` is deprecated for padding/radius. Use `ControlType.Padding` and `ControlType.BorderRadius` for new components.

Only use `FusedNumber` when preserving an existing component's saved prop shape or when a project already depends on fused numeric fields.

```tsx
padding: {
    type: ControlType.Padding,
    title: "Padding",
    defaultValue: "12px 16px",
}
```

Legacy-compatible resolver:

```tsx
function resolvePadding(props: any) {
    if (typeof props.padding === "string") return props.padding

    if (props.paddingIsMixed) {
        return `${props.paddingTop ?? 0}px ${props.paddingRight ?? 0}px ${props.paddingBottom ?? 0}px ${props.paddingLeft ?? 0}px`
    }

    return `${props.padding ?? 0}px`
}
```

## Hidden Logic

Controls can be hidden with a function that receives current props and returns a boolean.

```tsx
mode: {
    type: ControlType.Enum,
    options: ["static", "animated"],
    defaultValue: "static",
}
transition: {
    type: ControlType.Transition,
    hidden: (props) => props.mode !== "animated",
}
```

When wrapping controls with shared hidden logic, preserve each control's original `hidden`.

```tsx
function withHidden(controls: Record<string, any>, shouldHide: (props: any) => boolean) {
    return Object.fromEntries(
        Object.entries(controls).map(([key, control]) => {
            const originalHidden = control.hidden
            return [
                key,
                {
                    ...control,
                    hidden: (props: any) => {
                        if (shouldHide(props)) return true
                        return typeof originalHidden === "function"
                            ? originalHidden(props)
                            : Boolean(originalHidden)
                    },
                },
            ]
        })
    )
}
```

## Prop Migration

Never rely on raw props when evolving a component. Resolve props into a stable runtime config.

```tsx
function resolveConfig(props: any) {
    return {
        // Old flat prop to new grouped prop
        gap: props.appearance?.gap ?? props.gap ?? 8,

        // Old fused radius to new CSS radius string
        radius:
            props.appearance?.radius ??
            (typeof props.radius === "string" ? props.radius : `${props.radius ?? 8}px`),

        // Type-safe legacy enum/boolean migration
        mode:
            typeof props.mode === "boolean"
                ? props.mode
                    ? "enabled"
                    : "disabled"
                : props.mode ?? "enabled",
    }
}
```

Safe rename rule:

- Keep old keys in `defaultProps`.
- Remove old keys from `addPropertyControls` so new instances use the new schema.
- Read both old and new keys in `resolveConfig`.
- Do not reset or delete user data unless explicitly asked.

## Panel Design Guidelines

- Prefer segmented controls for 2-5 common choices.
- Prefer icon tabs for common visual concepts like alignment, mode, direction, and layout view.
- Prefer dropdown enums for long or text-heavy lists.
- Prefer `ControlType.Object` when related style controls would otherwise clutter the panel.
- Prefer modern style controls: `Padding`, `BorderRadius`, `Border`, `BoxShadow`, `Gap`, `Font`.
- Avoid putting every style token at top level unless the component is intentionally low-level.
- Keep titles short: `Fill`, `Text`, `Radius`, `Border`, `Gap`, `Font`, `Mode`.
- Use `hidden()` aggressively for irrelevant controls, but keep runtime config resilient.

## Checklist

- [ ] Are `defaultProps` synchronized with key `defaultValue`s?
- [ ] Are all legacy props resolved through a stable config function?
- [ ] Are new padding/radius controls using `Padding` and `BorderRadius` instead of `FusedNumber`?
- [ ] Are icon-tab enum controls used for alignment/mode/direction/view controls?
- [ ] Are long enum lists kept as dropdowns instead of cramped segmented controls?
- [ ] Are `optionTitles` present when `optionIcons` are used?
- [ ] Are `Array` controls capped with `maxCount`?
- [ ] Are object controls used for coherent style groups?
- [ ] Are hidden controls still handled by runtime fallbacks?
- [ ] Are secret-like values kept out of client-side Code Components?
