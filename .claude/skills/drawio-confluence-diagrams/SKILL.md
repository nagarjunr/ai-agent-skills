---
name: drawio-confluence-diagrams
description: >-
  Rules for generating Draw.io (mxfile XML) diagrams and publishing them to Confluence as attachment-based drawio macros. Covers box and edge styles, orthogonal routing, straight-line layout, label visibility and pre-upload verification. Use when creating or editing architecture, C4 or deployment diagrams for Confluence pages.
compatibility: Requires the drawio desktop CLI for rendering checks and a Confluence MCP server or API with attachment upload.
---

# Draw.io Diagram Formatting Guidelines

## Publishing to Confluence

### Correct approach: attachment-based macro

Draw.io diagrams in Confluence are **not** embedded inline. The diagram XML must be uploaded as a file attachment, then referenced by name in the macro.

**Step 1 — Upload the `.drawio` file as an attachment:**

```text
upload_attachment(  # tool name depends on your MCP server
  page_id="<page_id>",
  file_base64="<base64 encoded mxfile XML>",
  file_name="my-diagram.drawio",
  mime_type="application/vnd.jgraph.mxfile"
)
```

**Step 2 — Reference it in the page with the `drawio` macro:**

```xml
<ac:structured-macro ac:name="drawio" ac:schema-version="1" ac:macro-id="<unique-id>">
  <ac:parameter ac:name="border">true</ac:parameter>
  <ac:parameter ac:name="diagramName">my-diagram.drawio</ac:parameter>
  <ac:parameter ac:name="simpleViewer">false</ac:parameter>
  <ac:parameter ac:name="links">auto</ac:parameter>
  <ac:parameter ac:name="tbstyle">top</ac:parameter>
  <ac:parameter ac:name="lbox">true</ac:parameter>
  <ac:parameter ac:name="diagramWidth">960</ac:parameter>
  <ac:parameter ac:name="height">680</ac:parameter>
  <ac:parameter ac:name="revision">1</ac:parameter>
</ac:structured-macro>
```

**Important:** If a file with the same name already exists, uploading again returns an error. Use a versioned name (e.g. `my-diagram-v2.drawio`) for updates.

---

## Draw.io XML File Structure

Always wrap the `mxGraphModel` in an `mxfile` envelope with a `diagram` element:

```xml
<mxfile host="drawio">
  <diagram name="Diagram Title" id="diagram-id">
    <mxGraphModel dx="1422" dy="762" grid="1" gridSize="10" guides="1"
      tooltips="1" connect="1" arrows="1" fold="1" page="1"
      pageScale="1" pageWidth="1169" pageHeight="827" math="0" shadow="0">
      <root>
        <mxCell id="0"/>
        <mxCell id="1" parent="0"/>
        <!-- shapes and edges here -->
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

---

## Box (Vertex) Style Rules

### No rounded corners — always use sharp rectangles

```xml
style="whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;fontStyle=1;fontSize=12;"
```

- **Do NOT** include `rounded=1`
- Omitting `rounded` or using `rounded=0` gives sharp corners

### Standard color palette

| Role | Fill | Stroke |
| --- | --- | --- |
| Primary / Core | `#dae8fc` | `#6c8ebf` |
| Configuration / Repos | `#fff2cc` | `#d6b656` |
| Setup / One-time | `#f8cecc` | `#b85450` |
| Output / Targets | `#d5e8d4` | `#82b366` |
| Pipelines / Workflows | `#e1d5e7` | `#9673a6` |
| Tools / Runtime | `#f0f0f0` | `#666666` |

---

## Edge (Connector) Style Rules

### Mandatory edge style settings

Every edge must include:

```text
edgeStyle=orthogonalEdgeStyle;   ← right-angle (orthogonal) routing
rounded=0;                        ← sharp corners at bends, never curved
jumpStyle=arc;                    ← arc jump when two lines cross
jumpSize=10;                      ← size of the arc jump
```

Full example:

```xml
<mxCell id="e1" edge="1" source="2" target="3" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

### Explicit exit and entry points

Use `exitX/exitY` and `entryX/entryY` to control exactly where lines attach to boxes:

- Values are fractions: `0`=left/top, `0.5`=center, `1`=right/bottom
- Always include `exitDx=0;exitDy=0;entryDx=0;entryDy=0;`

```xml
style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;
       exitX=0.5;exitY=1;exitDx=0;exitDy=0;
       entryX=0.5;entryY=0;entryDx=0;entryDy=0;"
```

---

## Routing Rules

### Rule 1: Lines must never overlap boxes

Route lines around boxes using explicit waypoints. Plan exit/entry points so the path stays in clear space between boxes.

### Rule 2: L-shaped connections (one bend only)

Cross-hierarchy connections should have exactly ONE bend — either horizontal-then-vertical or vertical-then-horizontal. The bend should be roughly at the midpoint between source and target.

```xml
<!-- catalog (right=200) → lz (left=240): exit right, bend in gap at x=270, enter lz left -->
<mxCell id="14" edge="1" source="3" target="7" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;
         exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="270" y="210"/>
      <mxPoint x="270" y="370"/>
    </Array>
  </mxGeometry>
</mxCell>
```

### Rule 3: Tree fanout — shared trunk pattern

When one box connects to multiple children, all lines must share a **common trunk** that exits the source at one point, drops to a shared **bus line** at the midpoint y between source and children, then branches to each child's center. This creates the clean tree-diagram look.

**Bus y = midpoint between source bottom and children top.**

```xml
<!-- All 4 children connect via shared trunk at (480,100) → bus at y=140 -->

<!-- → catalog (center x=120) -->
<mxCell id="9" edge="1" source="2" target="3" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;
         exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="480" y="140"/>   <!-- shared trunk bottom -->
      <mxPoint x="120" y="140"/>   <!-- bus to catalog center -->
    </Array>
  </mxGeometry>
</mxCell>

<!-- → blueprints (center x=420) -->
<mxCell id="10" edge="1" source="2" target="4" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;
         exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="480" y="140"/>
      <mxPoint x="420" y="140"/>
    </Array>
  </mxGeometry>
</mxCell>
```

**Key:** All edges use `exitX=0.5;exitY=1` (same exit point) and their first waypoint is the same `(trunk_x, bus_y)`. The lines visually merge into one trunk.

### Rule 4: Straight lines for vertically aligned boxes

When source and target centers share the same x-coordinate, use no waypoints and matching `exitX`/`entryX` values:

```xml
<!-- blueprints (center x=420) → lz (center x=420): perfectly straight -->
<mxCell id="13" edge="1" source="4" target="7" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;jumpStyle=arc;jumpSize=10;
         exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

---

## Layout Rules

### Align children under the parent for straight vertical connections

Position children boxes so their center x falls **within** the parent box's x range. This avoids horizontal jogs on what should be straight vertical lines.

Example: parent at x=300, w=360 (range 300–660). Place children centers at x=420 and x=620 — both within 300–660. The exit fractions become:

- child at x=420: `exitX=(420-300)/360=0.33`
- child at x=620: `exitX=(620-300)/360=0.89`

### Keep gaps between sibling boxes for routing space

Always leave at least 40–60px gap between sibling boxes. Lines connecting non-adjacent rows route through these gaps.

---

## Waypoints Reference

Add explicit waypoints using an `Array` inside the edge's `mxGeometry`:

```xml
<mxGeometry relative="1" as="geometry">
  <Array as="points">
    <mxPoint x="270" y="210"/>
    <mxPoint x="270" y="370"/>
  </Array>
</mxGeometry>
```

The orthogonal router will honor these as pass-through points. Lines go from the source exit → each waypoint in order → target entry, making only right-angle turns.

---

## Straight Lines and Visible Labels (mandatory, takes precedence over Rule 2)

These rules override the L-shaped-connection guidance above. Bent lines are a last resort.

### Rule S1: Every connection is a straight line wherever possible

- Design the layout around the connections: connected boxes must overlap on one axis, so a single straight segment joins them.
  - Vertical neighbours: x-ranges overlap, pick one shared x inside the overlap, `exitX` and `entryX` give that same absolute x.
  - Horizontal neighbours: y-ranges overlap, pick one shared y inside the overlap, `exitY` and `entryY` give that same absolute y.
- Compute the exit and entry fractions from the shared coordinate: `fraction = (shared - box_origin) / box_size`. Do not default to 0.5 on both ends when the box centers differ, that produces a visible jog.
- Do not add waypoints to a connection that can be straight.
- A bend is acceptable only when a straight path would cross another box or edge and the layout cannot be changed. Then use exactly one bend with the long segment carrying the label.
- Connections to a container or group box: pick a coordinate inside the container's range and inside the source's range, so the line is still straight.
- Preferred generator pattern: write the diagram with a small script that places boxes, picks the shared coordinate automatically and fails when two connected boxes do not overlap on either axis.

### Rule S2: Edge labels are always fully visible

- The segment carrying a label must be long enough: horizontal edges need a gap of at least `len(label) * 6 + 30` px between the two boxes, vertical edges at least 44 px.
- Lengthen the line by widening the gap or moving the boxes. A larger diagram footprint is fine and preferred over a clipped or overlapping label.
- Keep labels short (a few words), use `labelBackgroundColor=#ffffff` so the line does not run through the text.
- Place the label's midpoint away from box borders and group borders: the label sits at the middle of the edge, so adjust the gap or the container sizes so no container border crosses that midpoint.
- Make the macro `diagramWidth` and `height` match the canvas so nothing is cut off.

### Rule S3: Verify before upload

1. Render with `drawio -x -f png -s 1 -o out.png file.drawio` and look at the result.
2. Check: no bent edges where a straight one is possible, every label readable, no label on top of a border or box, arrows land on the intended box.
3. Upload under a new file name (`-v2`, `-v3`) and use a new `macro-id`, then check the page.
4. Compare the uploaded file size with the local file size (the upload response shows `fileSize`) to catch encoding errors.

### Rule S4: Keep text and labels off borders, lines and boxes (avoid cut-over text)

- No edge, edge label or box text may be crossed or overlapped by another line, a container border or a container title. Check every label's bounding box against all container borders.
- Never route an arrow through a container title. Enter containers at a y or x coordinate that is clear of the title strip (title is at the top, about 30 px).
- Put external actors where their line to the target is short and crosses as few borders as possible. Typical layout: actors in a column on one side, each aligned with its target on the same axis (for example Internet above Administrators in the left column, each in line with its own firewall NIC), targets stacked in the same order. Do not stack two targets on one axis so that one line has to pass the other.
- A label sits at the middle of its edge by default. When container borders cross the middle, move the label along the edge with the edge geometry offset (`<mxGeometry x="-0.5" relative="1" as="geometry"/>`, range -1 source to +1 target) until it sits between two borders, or widen the gap so it fits there.
- Keep container titles short enough to fit on one line inside the container width, and leave at least 30 px between the title and the first box inside.
- Generator check (implement it in your diagram generator script): compute each label's bounding box and fail the build when it intersects the border of any container without being fully inside it.

- Label offset sign: in `<mxGeometry x="..." relative="1">` a positive value moves the label toward the edge's **target**, a negative value toward the **source**. For an edge that runs right to left (source on the right), move the label to the right with a negative value. Always render and look, a wrong sign puts the label back on the borders.

## Related

- `docs-update-verify-loop` for the page update and verification workflow.
- `confluence-storage-format` for the surrounding page markup.
