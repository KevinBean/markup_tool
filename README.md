# Cable Drawing Markup Editor

A browser-based PDF markup tool designed for cable engineers to efficiently modify, annotate, and communicate drawing changes to drafters. The tool enables visual manipulation of PDF drawings with support for moving regions, masking content, adding elements, and exporting to both PDF and DXF formats.

## Key Features

### PDF Handling
- **Load PDFs** via drag-and-drop or file picker
- **Multi-page support** with page navigation
- **High-quality rendering** at configurable DPI (default 150)
- **PDF Export** - Flatten all layers and export as a new PDF

### Markup Tools

#### Snippets
Snippets are rectangular regions "cut" from the PDF that can be repositioned:
- Select a region of the PDF to create a snippet
- Drag snippets to new positions
- Auto-creates a white mask at the original location
- **Snippet Library** - Save and reuse snippets across sessions (persisted to localStorage)

#### Masks
White rectangles used to hide/cover original PDF content:
- Draw rectangle masks with mouse drag
- Select, move, and resize existing masks
- Configurable fill color

#### Info Points
Clickable information markers for adding metadata to specific locations:
- Single-click placement
- Customizable label, description, color, and size
- Displays as a colored circle with "i" icon
- Full metadata editing in Properties Panel

#### Shape Tools
- **Line** - Draw straight lines
- **Rectangle** - Draw rectangles
- **Circle** - Draw circles (drag from center)
- **Arrow** - Draw directional arrows (planned)

#### Text & Annotations
- **Text** - Add text labels (planned)
- **Dimension** - Add measurement dimensions (planned)

### Element Library (Planned)
Pre-built cable engineering symbols:
- Joint Bay
- Link Box
- SVL (Sheath Voltage Limiter)
- Earth Point
- Termination
- Custom SVG import

### Export Options
- **PDF Export** - Flattened image embedded in new PDF
- **DXF Export** - Vector elements for CAD integration (planned)

## Installation

No installation required. The application is a single HTML file that runs entirely in the browser.

### Option 1: Direct File
Simply open `index.html` in a modern web browser (Chrome, Firefox, Edge).

### Option 2: Local Server
For development, you can serve it locally:
```bash
# Python 3
python -m http.server 8000

# Node.js (with http-server)
npx http-server
```

Then open `http://localhost:8000` in your browser.

## Usage

### Getting Started
1. Open the application in your browser
2. Load a PDF by:
   - Dragging and dropping a PDF file onto the canvas
   - Clicking "Open PDF" in the menu bar
3. Use the tools in the left panel to mark up the drawing
4. Export your work as PDF or save the project

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `V` | Select tool |
| `S` | Snippet tool |
| `M` | Mask tool |
| `L` | Line tool |
| `R` | Rectangle tool |
| `C` | Circle tool |
| `I` | Info Point tool |
| `Delete` / `Backspace` | Delete selected |
| `Escape` | Cancel/deselect |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` / `Ctrl+Shift+Z` | Redo |
| `Ctrl+S` | Save project |
| `Ctrl+O` | Open project |
| `Ctrl+E` | Export PDF |
| `0` | Fit to view |
| `1` | Zoom to 100% |
| `Space + Drag` | Pan canvas |
| `Scroll` | Zoom in/out |
| `Middle Mouse + Drag` | Pan canvas |

### Viewport Navigation
- **Pan**: Middle mouse button drag, or Space + left mouse drag
- **Zoom**: Mouse scroll wheel, or use zoom buttons
- **Fit to View**: Press `0` or use the Fit button

### Working with Snippets
1. Select the Snippet tool (`S`)
2. Click and drag to select a region of the PDF
3. The selected region becomes a movable snippet
4. A white mask is automatically created at the source location
5. Use the Select tool (`V`) to move snippets

### Using the Snippet Library
1. Select a snippet with the Select tool
2. Go to the "Library" tab in the left panel
3. Enter a name and click "Save to Library"
4. Saved snippets persist across browser sessions
5. Click "Insert" to add a library snippet to the current document

### Info Points
1. Select the Info Point tool (`I`)
2. Click anywhere on the canvas to place a point
3. The point is automatically selected
4. Use the Properties panel to add:
   - **Label**: Short text displayed above the point
   - **Description**: Detailed information/metadata
   - **Color**: Point fill color
   - **Size**: Point radius

## Architecture

### Layer System

| Layer | Z-Index | Content | Editable |
|-------|---------|---------|----------|
| Annotations | 500 | Clouds, stamps (planned) | Yes |
| Elements | 400 | Shapes, symbols, info points | Yes |
| Snippets | 300 | Moved PDF regions | Yes |
| Masks | 200 | White cover rectangles | Yes |
| PDF Base | 100 | Original PDF render | No |

### Project File Format
Projects are saved as `.cme` (Cable Markup Editor) files in JSON format:

```javascript
{
  "meta": {
    "version": "1.1",
    "created": "ISO8601 timestamp",
    "name": "Project Name"
  },
  "settings": {
    "styles": { /* default styles */ }
  },
  "pages": [{
    "pageNumber": 0,
    "pdfDataUrl": "base64 encoded PDF page",
    "width": 1190,
    "height": 841,
    "snippets": [ /* Snippet objects */ ],
    "masks": [ /* Mask objects */ ],
    "elements": [ /* Element objects */ ],
    "infoPoints": [ /* Info Point objects */ ]
  }]
}
```

## Data Models

### Snippet
```javascript
{
  id: "unique_id",
  type: "snippet",
  name: "Snippet 1",
  x: 100, y: 100,           // Current position
  width: 200, height: 150,
  sourceX: 100, sourceY: 100, // Original position
  visible: true,
  locked: false
}
```

### Info Point
```javascript
{
  id: "unique_id",
  type: "info_point",
  name: "Info 1",
  x: 150, y: 200,           // Position
  label: "Point A",         // Short label above point
  description: "Detailed information...",
  metadata: {},             // Custom key-value pairs
  color: "#0ea5e9",         // Point fill color
  radius: 8,                // Point size
  visible: true,
  locked: false
}
```

### Element (Shape)
```javascript
{
  id: "unique_id",
  type: "rectangle|circle|line",
  name: "Rectangle 1",
  // Geometry varies by type
  x: 100, y: 100, width: 50, height: 30,  // rectangle
  // or: x: 150, y: 150, radius: 25,       // circle
  // or: x1: 100, y1: 100, x2: 200, y2: 150, // line
  stroke: "#000000",
  strokeWidth: 2,
  fill: "none",
  visible: true,
  locked: false
}
```

## Browser Support

| Browser | Minimum Version |
|---------|-----------------|
| Chrome | 90+ |
| Firefox | 88+ |
| Edge | 90+ |
| Safari | 14+ |

## Dependencies

| Library | Purpose |
|---------|---------|
| [PDF.js](https://mozilla.github.io/pdf.js/) | PDF parsing and rendering |
| [pdf-lib](https://pdf-lib.js.org/) | PDF generation for export |
| [React](https://reactjs.org/) | UI framework (via CDN) |
| [Tailwind CSS](https://tailwindcss.com/) | Styling (via CDN) |

All dependencies are loaded from CDN - no build step required.

## Development Roadmap

### Phase 1: Core MVP (Current)
- [x] PDF loading and rendering
- [x] Viewport controls (pan, zoom)
- [x] Snippet creation and manipulation
- [x] Mask tool
- [x] PDF export
- [x] Project save/load
- [x] Info Point element type
- [x] Snippet Library

### Phase 2: Basic Drawing Tools
- [x] Line tool
- [x] Rectangle tool
- [x] Circle tool
- [ ] Arrow tool (UI exists)
- [ ] Text tool (UI exists)
- [ ] Element resize handles
- [ ] Element rotation

### Phase 3: Element Library
- [ ] Built-in cable symbols
- [ ] Symbol placement tool
- [ ] Custom SVG import
- [ ] Callout annotations
- [ ] Cloud/bubble markup

### Phase 4: DXF Export
- [ ] DXF file generation
- [ ] Layer mapping
- [ ] Symbol blocks
- [ ] Coordinate transformation

### Phase 5: Scale & Dimensions
- [ ] Scale calibration tool
- [ ] Dimension tool
- [ ] Measurement tool

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly in multiple browsers
5. Submit a pull request

## License

[Add license information]

## Acknowledgments

- PDF.js by Mozilla
- pdf-lib by Hopding
- Tailwind CSS

---

*For detailed specifications, see the Software Requirements Specification document.*
