---
name: framer-asset-sharing
description: Official Framer Documentation for Asset Sharing and Unlinking Prevention
---

# Framer Asset Sharing

All Components in Framer are built on ES Modules. Every Smart Component, Code Component, and Vector Set has a unique URL that can be shared.

## How to Share
1. Find your Component under **Assets → Code Component**.
2. Right-click and select **"Copy URL…"**.
3. Paste this URL into any Framer project directly onto the Canvas.

### Versions
URLs often contain a version ID (e.g., `https://framer.com/m/Button-5TDo.js@xyz`). This points to a specific snapshot of your component, ensuring stability even if you continue iterating on the primary version.

## Preventing Unlinking

To prevent users from unlinking a Code Component (which turns it into a Frame and loses code functionality), use the `@framerDisableUnlink` annotation.

```javascript
/**
 * @framerDisableUnlink
 *
 * @framerIntrinsicWidth 200
 * @framerIntrinsicHeight 200
 */
export default function MyComponent(props) {
    // ...
}
```

> [!CAUTION]
> Preventing unlink does not prevent your code from being seen. As with any webpage, the code can be extracted via developer tools. Do not store sensitive information (API keys, etc.) directly in Code Components.
