# API Reference

## FreetextHighlight Component

A draggable, editable text annotation component for PDF documents.

### Import

```tsx
import { FreetextHighlight } from "react-pdf-highlighter-extended";
```

### Props

```typescript
interface FreetextHighlightProps {
  /**
   * The highlight to be rendered.
   */
  highlight: ViewportHighlight;

  /**
   * Callback triggered when the highlight position changes (drag).
   * @param rect - The updated highlight area with page number.
   */
  onChange?(rect: LTWHP): void;

  /**
   * Callback triggered when the text content is updated.
   * @param newText - The updated text content.
   */
  onTextChange?(newText: string): void;

  /**
   * Callback triggered when style changes via the built-in style panel.
   * @param style - The updated style options.
   */
  onStyleChange?(style: FreetextStyle): void;

  /**
   * Whether the highlight has been auto-scrolled into view.
   * Default styling renders a red border when true.
   */
  isScrolledTo?: boolean;

  /**
   * Bounds for the draggable area. Prevents moving the highlight
   * off the viewer/page. See react-rnd documentation.
   */
  bounds?: string | Element;

  /**
   * Callback triggered on right-click.
   */
  onContextMenu?(event: MouseEvent<HTMLDivElement>): void;

  /**
   * Called when the user starts editing (drag or text edit).
   */
  onEditStart?(): void;

  /**
   * Called when the user finishes editing.
   */
  onEditEnd?(): void;

  /**
   * Custom CSS styles for the container.
   */
  style?: CSSProperties;

  /**
   * Text color for the annotation content.
   * @default "#333333"
   */
  color?: string;

  /**
   * Background color for the annotation content.
   * @default "#ffffc8"
   */
  backgroundColor?: string;

  /**
   * Font family for the annotation content.
   * @default "inherit"
   */
  fontFamily?: string;

  /**
   * Font size for the annotation content.
   * @default "14px"
   */
  fontSize?: string;

  /**
   * Custom drag handle icon. Replaces the default 6-dot grid icon.
   */
  dragIcon?: ReactNode;

  /**
   * Custom edit button icon. Replaces the default pencil icon.
   */
  editIcon?: ReactNode;

  /**
   * Custom style button icon. Replaces the default palette icon.
   */
  styleIcon?: ReactNode;

  /**
   * Custom background color presets for the style panel.
   * @default ["#ffffc8", "#ffcdd2", "#c8e6c9", "#bbdefb", "#e1bee7"]
   */
  backgroundColorPresets?: string[];

  /**
   * Custom text color presets for the style panel.
   * @default ["#333333", "#d32f2f", "#1976d2", "#388e3c", "#7b1fa2"]
   */
  textColorPresets?: string[];
}
```

### FreetextStyle Type

```typescript
interface FreetextStyle {
  color?: string;
  backgroundColor?: string;
  fontFamily?: string;
  fontSize?: string;
}
```

### Usage Example

```tsx
<FreetextHighlight
  highlight={highlight}
  isScrolledTo={isScrolledTo}
  onChange={(boundingRect) => {
    // Save new position
    editHighlight(highlight.id, {
      position: {
        boundingRect: viewportToScaled(boundingRect),
        rects: [],
      },
    });
  }}
  onTextChange={(newText) => {
    // Save new text
    editHighlight(highlight.id, {
      content: { text: newText },
    });
  }}
  onStyleChange={(style) => {
    // Save style changes from the built-in style panel
    editHighlight(highlight.id, style);
  }}
  onEditStart={() => toggleEditInProgress(true)}
  onEditEnd={() => toggleEditInProgress(false)}
  color={highlight.color ?? "#333333"}
  backgroundColor={highlight.backgroundColor ?? "#ffffc8"}
  fontFamily={highlight.fontFamily ?? "inherit"}
  fontSize={highlight.fontSize ?? "14px"}
/>
```

### Usage with Custom Icons

```tsx
<FreetextHighlight
  highlight={highlight}
  dragIcon={<GripVertical size={14} />}
  editIcon={<Pencil size={14} />}
  styleIcon={<Palette size={14} />}
  // ... other props
/>
```

### Usage with Custom Color Presets

```tsx
<FreetextHighlight
  highlight={highlight}
  backgroundColorPresets={["#fff3e0", "#e3f2fd", "#f3e5f5", "#e8f5e9"]}
  textColorPresets={["#000000", "#1565c0", "#6a1b9a", "#2e7d32"]}
  onStyleChange={(style) => editHighlight(highlight.id, style)}
/>
```

---

## PdfHighlighter Props (Freetext-related)

### enableFreetextCreation

```typescript
enableFreetextCreation?(event: MouseEvent): boolean;
```

Condition to check before a freetext annotation click is registered.
When this returns `true`, clicking on the PDF will create a freetext annotation.

**Example:**
```tsx
<PdfHighlighter
  enableFreetextCreation={() => freetextMode}
  // ...
/>
```

### onFreetextClick

```typescript
onFreetextClick?(position: ScaledPosition): void;
```

Callback triggered when user clicks to create a freetext annotation.
Provides the scaled position where the click occurred.

**Example:**
```tsx
const handleFreetextClick = (position: ScaledPosition) => {
  const newHighlight = {
    id: generateId(),
    type: "freetext",
    position,
    content: { text: "New note" },
  };
  setHighlights([newHighlight, ...highlights]);
};

<PdfHighlighter
  onFreetextClick={handleFreetextClick}
  // ...
/>
```

---

## Types

### HighlightType

```typescript
type HighlightType = "text" | "area" | "freetext";
```

The type of highlight. Use this to determine which component to render:
- `"text"` - TextHighlight for selected text
- `"area"` - AreaHighlight for rectangular regions
- `"freetext"` - FreetextHighlight for text annotations

### LTWHP

```typescript
type LTWHP = {
  left: number;    // X coordinate of top-left
  top: number;     // Y coordinate of top-left
  width: number;   // Width of rectangle
  height: number;  // Height of rectangle
  pageNumber: number;  // 1-indexed page number
};
```

A rectangle as measured by the viewport, including page number.

### ScaledPosition

```typescript
type ScaledPosition = {
  boundingRect: Scaled;
  rects: Array<Scaled>;
  usePdfCoordinates?: boolean;
};
```

Position of a highlight with normalized coordinates.
Suitable for storage and retrieval across different viewport sizes.

### ViewportHighlight

```typescript
type ViewportHighlight<T extends Highlight = Highlight> = Omit<T, "position"> & {
  position: ViewportPosition;
};
```

A highlight with position converted to viewport coordinates for rendering.
