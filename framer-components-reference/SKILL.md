---
name: framer-components-reference
description: Official Framer Library Reference (RenderTarget, Localization, addPropertyControls)
---

# Framer Library API Reference

A list of exports from the `framer` library available in every Code Component.

## RenderTarget

To detect which context your Code Component is currently being shown, you can import and use `RenderTarget`.

```javascript
import { RenderTarget } from "framer"

const currentTarget = RenderTarget.current()

// Available Targets:
// RenderTarget.canvas    — The Framer Canvas (Design mode)
// RenderTarget.preview   — The Preview window or live site
// RenderTarget.export    — The Export Canvas (during static export)
// RenderTarget.thumbnail — Project Thumbnails

const isOnCanvas = RenderTarget.current() === RenderTarget.canvas
const isPreview = RenderTarget.current() === RenderTarget.preview

### useIsOnFramerCanvas
The `useIsOnFramerCanvas` hook is a shorthand to check if your code is running on the Framer Canvas.

```javascript
import { useIsOnFramerCanvas } from "framer"

export default function MyComponent() {
    const isOnCanvas = useIsOnFramerCanvas()
    return <div>{isOnCanvas ? "Canvas" : "Preview"}</div>
}
```
```

### Canvas and Export (Static Renderer)
For Components that animate, like WebGL shaders, we have a custom hook that we recommend you use instead. This ensures that your Component is static both on the Canvas and the exported assets—preventing performance issues.

```javascript
import { useIsStaticRenderer } from "framer"

// Returns true on both Canvas and Export
const isStaticRenderer = useIsStaticRenderer()

if (isStaticRenderer) {
    // Render static version
}
```

## Localization

API for getting and setting the current Locale info.

### useLocaleInfo()
Returns the active locale, available locales, and a function to change the locale.

```javascript
import { useLocaleInfo } from "framer"

export function LocalePicker() {
    const { activeLocale, locales, setLocale } = useLocaleInfo()
    // activeLocale properties:
    // - id: Locale ID ('default' or e.g. 'otsmaRta5')
    // - code: ISO 639 language code (e.g. 'en')
    // - slug: URL slug (e.g. '/en')
    // - name: Custom name defined in Framer (e.g. 'English')
    // ...
}
```

### useLocaleCode()
A shorthand hook to get only the active language code.

```javascript
import { useLocaleCode } from "framer"

export default function MyComponent() {
    const localeCode = useLocaleCode() // e.g. "en"
    return <div>{localeCode}</div>
}
```

## Property Controls

`addPropertyControls` is used to expose props to the Framer UI.

```javascript
import { addPropertyControls, ControlType } from "framer"

function Button(props) {
    const { tint = "#09F" } = props
    return <button style={{ background: tint }}>Hello</button>
}

addPropertyControls(Button, {
    tint: { type: ControlType.Color }
})
```

## Framer Motion

The entire `framer-motion` API is available and can be imported directly.

```javascript
import { animate, motion } from "framer-motion"

export function AnimatedBox() {
    return <motion.div animate={{ scale: 2 }} />
}
```
