---
name: framer-auto-sizing
description: Official Framer Documentation for Component Auto-Sizing and Layout Annotations
---

# Framer Auto-Sizing

Framer can accurately measure any content on the canvas. You can define how your Code Components should behave in terms of sizing using special annotations.

## Layout Options

There are four settings for code component layout, which can be set for width or height axis individually using `@framerSupportedLayoutWidth` and `@framerSupportedLayoutHeight`.

- **`auto`**: The component dictates its own size based on its content.
- **`fixed`**: The component fills 100% of its container's size.
- **`any`**: Users can switch between `auto` and `fixed` sizing in the properties panel.
- **`any-prefer-fixed`**: Same as `any`, but defaults to `fixed`.

### Example
```javascript
/**
 * @framerSupportedLayoutWidth auto
 * @framerSupportedLayoutHeight fixed
 */
export function Toggle(props) {
    // ...
}
```

## Intrinsic Size
Define the default size when a component is first inserted onto the canvas or when fixed sizing is enabled.

```javascript
/**
 * @framerIntrinsicHeight 200
 * @framerIntrinsicWidth 200
 */
export function Box(props) {
    // ...
}
```

## Supporting Fixed Sizing
By spreading the `style` prop into your parent container, Framer can override width and height when fixed sizing is used.

```javascript
export function MyComponent({ style, ...props }) {
    return (
        <div style={{ ...style, backgroundColor: "red" }}>
            {/* Content */}
        </div>
    )
}
```

## Advanced Sizing

### useMeasuredSize
If you need to know the actual pixel size of your component at runtime, use the `useMeasuredSize` hook.

```javascript
import { useMeasuredSize } from "https://framer.com/m/framer/useMeasuredSize.js"
import { useRef } from "react"

export function ScaledComponent(props) {
    const ref = useRef(null)
    const size = useMeasuredSize(ref)
    
    const width = size?.width ?? 0
    
    return (
        <div ref={ref} style={{ width: "100%", height: width * 0.5 }}>
            Measured Width: {width}
        </div>
    )
}
```

> [!NOTE]
> For components that animate (like WebGL), use `useLayoutEffect` to handle size updates to ensure the measuring system captures changes correctly.
