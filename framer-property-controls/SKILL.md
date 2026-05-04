---
name: framer-property-controls
description: Complete Engineering Guide to Framer Property Controls (Official & Undocumented)
---

# Framer Property Controls: The Professional Engineering Standard

Property Controls are the interface between your code and the Framer UI. This guide combines full official documentation, **Framer Developer Gems**, and advanced production patterns for migration and scaling.

## 1. General Implementation
Controls are added using `addPropertyControls(Component, schema)`. Always define `defaultProps` to ensure a consistent experience when the component is first dragged onto the canvas.

```javascript
import { addPropertyControls, ControlType } from "framer"

export function MyComponent(props) {
    return <div style={{ color: props.color }}>{props.text}</div>
}

// ALWAYS define defaultProps for canvas stability
MyComponent.defaultProps = {
    text: "Hello World!",
    color: "#000000",
}

addPropertyControls(MyComponent, {
    text: {
        type: ControlType.String,
        title: "Display Text",
        defaultValue: "Hello World!",
    },
    color: {
        type: ControlType.Color,
        title: "Text Color",
    },
})
```

## 2. Official Control Types (Deep Dive)

### 2.1 Array Control (`ControlType.Array`)
*Headline: "Managing Dynamic Lists and Children"*
Allows multiple values for a specific control type, returned as a standard JavaScript array.
```javascript
addPropertyControls(MyComponent, {
    images: {
        type: ControlType.Array,
        control: { type: ControlType.ResponsiveImage },
        maxCount: 5,
    },
    children: {
        type: ControlType.Array,
        control: { type: ControlType.ComponentInstance },
        maxCount: 10,
    }
})
```

### 2.2 Object Control (`ControlType.Object`)
*Headline: "Grouping Related Properties"*
Groups multiple properties into a single object for a cleaner UI and easier prop management.
```javascript
addPropertyControls(MyComponent, {
    style: {
        type: ControlType.Object,
        controls: {
            opacity: { type: ControlType.Number, min: 0, max: 1, step: 0.1 },
            tint: { type: ControlType.Color },
        }
    }
})
```

### 2.3 Enum & Segmented Control (`ControlType.Enum`)
*Headline: "High-Density Option Selection"*
Creates a dropdown or segmented control for predefined choices.
```javascript
addPropertyControls(MyComponent, {
    variant: {
        type: ControlType.Enum,
        options: ["primary", "secondary", "tertiary"],
        optionTitles: ["Primary Button", "Secondary", "Minimal"],
        displaySegmentedControl: true,
        segmentedControlDirection: "horizontal", // or "vertical" for tight spaces
    }
})
```

## 3. Advanced Interaction Logic (ThreeJS Runtime Patterns)

### 3.1 Top-Level Role Switching
*Headline: "Multi-Role Component Architecture"*
For components that act as both a "Slot" and a "Root" (Coordinator), use a wrapper function to globally hide entire sets of controls based on a `role` prop.
```javascript
function _withRoleHidden(controls, shouldHide) {
    const mapped = {}
    Object.entries(controls).forEach(([key, control]) => {
        const originalHidden = control.hidden
        mapped[key] = {
            ...control,
            hidden: (props) => {
                if (shouldHide(props)) return true
                return typeof originalHidden === "function" ? originalHidden(props) : !!originalHidden
            }
        }
    })
    return mapped
}
```

### 3.2 Nested Conditional Visibility
*Headline: "Contextual Property Exposure"*
Use functional `hidden` to create deep dependency trees where child controls only appear when their parents are active.
```javascript
addPropertyControls(MyComponent, {
    enableParticles: { type: ControlType.Boolean, defaultValue: false },
    particleConfig: {
        type: ControlType.Object,
        hidden: (p) => !p.enableParticles,
        controls: {
            speed: { type: ControlType.Number, hidden: (p) => p.mode === "static" }
        }
    }
})
```

## 4. Prop Migration & Versioning (Prevention of Property Loss)

### 4.1 The "Resolver" Pattern
*Headline: "Zero-Breakage Component Refactoring"*
Never use `props` directly in your component logic. Always pass them through a `resolveConfig` function that handles legacy fallbacks when you rename or move properties.

```javascript
function resolveConfig(props) {
    return {
        // Migration: Old flat prop 'fov' to new 'camera.fov' object
        fov: props.camera?.fov ?? props.fov ?? 35,
        
        // Migration: Old 'modelUrlProp' to new 'modelUrl'
        modelUrl: props.modelUrl ?? props.modelUrlProp ?? "",
        
        // Defaulting for non-existing props
        intensity: props.intensity ?? 1,
    }
}
```

### 4.2 Safe Rename Strategy
*Headline: "Non-Destructive Schema Evolution"*
When renaming a property, keep the old property in `defaultProps` but remove it from `addPropertyControls`. This allows old instances to continue working while forcing new instances to use the new schema.

### 4.3 Type-Defensive Migration
*Headline: "Safe Property Type Conversion"*
When changing a property type (e.g., from `String` to `Number` or `Enum`), the underlying project metadata might still hold the old type. Always cast your values in the resolver.

```javascript
function resolveTypeSafeConfig(props) {
    const env = props.environment || {}
    return {
        // CASE: Migrating String (old Enum) to Number
        // Pattern: parseInt/parseFloat with a reliable fallback
        shadowMapSize: parseInt(env.shadowSize ?? "1024", 10) || 1024,
        
        // CASE: Migrating Boolean to String/Enum
        // Pattern: Strict type check to handle legacy boolean values
        mode: typeof props.mode === "boolean" 
            ? (props.mode ? "active" : "inactive") 
            : props.mode ?? "active",
            
        // CASE: Numeric safety
        // Pattern: Ensuring values stay within new constraints
        opacity: Math.max(0, Math.min(1, Number(props.opacity ?? 1))),
    }
}
```

## 5. Complex Enum & Array Patterns

### 5.1 Recursive Data Structures
*Headline: "Scene Hierarchy Management"*
Handling lists of complex objects (e.g., Sub-Scenes) requires recursive `Object` nesting inside an `Array`.
```javascript
scenes: {
    type: ControlType.Array,
    control: {
        type: ControlType.Object,
        controls: {
            modelUrl: { type: ControlType.String },
            visible: { type: ControlType.Boolean, defaultValue: true },
        }
    }
}
```

## 6. Undocumented Power User Features (Developer Gems)

### 6.1 Directional Icons in Enums
*Headline: "Context-Aware UI Controls"*
Provide icons that map to different options based on the component's orientation or state.
```javascript
alignment: {
    type: ControlType.Enum,
    options: ["flex-start", "center", "flex-end"],
    optionIcons: {
        directions: {
            right: ["align-top", "align-middle", "align-bottom"],
            left: ["align-top", "align-middle", "align-bottom"],
            top: ["align-left", "align-center", "align-right"],
            bottom: ["align-left", "align-center", "align-right"],
        },
    },
    defaultValue: "center",
    displaySegmentedControl: true,
}
```

### 6.2 Premium Object Customization
*Headline: "Premium UI for Nested Data"*
Enhance the `Object` control with undocumented UX features:
- **`optional: true`**: Adds a toggle to remove/add the object, showing an "Add..." button.
- **`buttonTitle`**: Customizes the text on the "Add" button.
- **`icon`**: Adds a specialized icon next to the object title (`boolean`, `object`, `color`, `effect`, `interact`).

## 7. Engineering Best Practices & Edge Cases

### 7.1 Visual Grouping & Spacing
*Headline: "The Divider Hack"*
Framer doesn't have a native horizontal divider. Use the `description` of a `String` control to create visual separation.
```javascript
separator: {
    type: ControlType.String,
    title: " ",
    description: "⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯", 
    hidden: () => true 
}
```

### 7.2 Performance Budgeting
*Headline: "UI Responsiveness and Array Capping"*
Large arrays (10+) of complex `Object` controls can cause significant lag in the Framer Property Panel. Always set a strict `maxCount` to maintain editor performance.

## 8. Implementation Checklist
- [ ] Are legacy props handled in a `resolveConfig` function to prevent breakage?
- [ ] Are type changes handled with explicit casting (`parseInt`, `Number()`)?
- [ ] Are `defaultProps` synchronized with `defaultValue`?
- [ ] Is complex logic hidden behind `hidden()` functions?
- [ ] Does the `Array` control have a `maxCount` for performance?
- [ ] Are dividers used to separate logical sections?
- [ ] Is `role`-based hiding applied for multi-purpose components?
