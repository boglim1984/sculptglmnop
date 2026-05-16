# SculptGL Boundary Brush Reboot Handoff

**Date/Time:** 05/16/2026 12:46 PM EDT  
**Source Chat:** https://chatgpt.com/c/6a06d242-1d94-83ea-8c69-36f52b899e3e  
**Repo:** `boglim1984/sculptglmnop`  
**Purpose:** Archive the SculptGL reboot ideas from the beginning of this chat because the active conversation shifted into the GCR / registry scheduler system. This file preserves the original SculptGL project direction so it can be handed off cleanly to a future chat or coding agent.

---

## 1. Why this file exists

This chat originally carried early planning for a SculptGL-based mesh-color boundary tool. During the same conversation, the work pivoted into the Code Projects Registry, scheduler, GCR queue lifecycle, and actuator-hardening system. Because the chat title and ongoing context now belong to the registry system, the SculptGL material needed to be extracted into a durable handoff file.

This document is not an implementation spec yet. It is a reboot archive: enough structured memory for a new chat, Codex, AG, or another agent to restart the SculptGL project without needing to re-read the entire mixed-purpose conversation.

---

## 2. Project seed

The user forked SculptGL and wanted to begin a new code project around a mesh painting / cleanup tool. The working repo name in GitHub is:

```text
boglim1984/sculptglmnop
```

The project concept is to modify or extend SculptGL/WebGL-style mesh editing so it can clean jagged color/material boundaries on triangulated meshes. The first tool is not a full sculpting replacement. It is a targeted boundary brush.

---

## 3. Core tool idea: two-color boundary smoothing brush

The desired first tool:

```text
A brush that selects or acts on faces/vertices along a border where two painted colors meet.
It detects the jagged triangular boundary between two color islands.
It approximates a smoother boundary between those two colors.
It lets the user paint along vertex-color island boundaries so the transition becomes cleaner after the brush pass.
```

The user described this as a tool that can work with “two colors along a border that they meet,” then approximate a smooth boundary between the two. The visual goal is not necessarily geometric smoothing of the mesh itself; it is cleanup of the color/material separation line across mesh topology.

This relates directly to the user’s broader 3D printing workflow, where vertex color / multi-material boundaries can become streaky or jagged after slicing.

---

## 4. Why SculptGL

SculptGL is attractive here because it is already a lightweight WebGL mesh editing environment with brush interaction patterns. The idea is to graft a specialized color-boundary cleanup workflow into an existing interactive mesh viewer/editor rather than building the whole viewer from scratch.

Initial project scope should remain narrow:

```text
load mesh
show vertex/face colors
detect local two-color boundary under brush
propose or apply cleaned boundary classification
preview result
export modified mesh/color data
```

Avoid turning the reboot into a whole new sculpting app before the boundary brush proves itself.

---

## 5. Boundary brush behavior model

The boundary brush should think locally and visually:

1. User paints or drags along a jagged border between two color regions.
2. Tool samples nearby faces/vertices under the brush radius.
3. Tool identifies the two dominant color/material labels in that local region.
4. Tool estimates a cleaner boundary curve or decision line between them.
5. Tool reassigns nearby ambiguous faces/vertices to one side or the other.
6. Strength controls how aggressively it simplifies the border.
7. Result should reduce isolated slivers, one-off streaks, and stair-stepped triangle artifacts.

Possible implementation models to test later:

```text
local majority voting
shortest/smoothest separating path across triangle adjacency
graph cut / min-cut style label refinement
signed distance to fitted curve
Laplacian-like smoothing of scalar color label field
morphological open/close operations on face-label islands
boundary snap-to-smooth-curve within brush radius
```

Do not assume the first version needs the most mathematically advanced method. The project should be test-driven with visual before/after images.

---

## 6. Relationship to 3D printing / Bambu vertex-color workflow

The underlying pain point is multi-color 3D printing. The user uses vertex color in Bambu Studio or related slicer workflows to compute slicing into multiple filaments. Jagged or streaky color boundaries can produce ugly lone lines and broken flow between materials.

The boundary brush is meant to act before slicing:

```text
mesh has messy painted color/material boundary
boundary brush cleans labels/vertex colors locally
export cleaned mesh
slicer receives smoother material separation
printed object has cleaner color transitions
```

The tool should eventually be evaluated against screenshots or renders that mimic the slicer-visible effect, not only against internal mesh statistics.

---

## 7. Proposed testing setup

A key instruction from the chat was to create a testing setup using Blender and sample geometry:

```text
Use Blender to generate sample meshes with two color/material regions.
Intentionally make the boundary jagged with triangular faces.
Run the boundary brush at different strengths/methods.
Use a screenshot/render rig to compare before and after.
Use visual contact sheets similar to the previous Blender mesh Boolean project.
```

The testing harness should create repeatable cases rather than relying only on hand inspection inside SculptGL.

Test assets should include:

```text
flat plane with jagged diagonal two-color boundary
curved surface with jagged boundary
sphere or bust-like surface with painted island edge
thin sliver artifacts crossing boundary
isolated one-face color islands near boundary
noisy stair-step boundary on low-poly triangles
```

The evaluation goal is visual clarity: before/after renders that make it obvious whether the boundary was improved or damaged.

---

## 8. Screenshot/contact-sheet review workflow

The user specifically connected this to the previous Blender color mesh Boolean project’s screenshot rig. The desired pattern:

```text
for each trial:
  generate or load a controlled mesh
  apply boundary cleanup method and strength
  render before view
  render after view
  optionally render boundary overlay / difference view
  assemble contact sheet
  archive parameters and images
```

This allows ChatGPT or another vision-capable reviewer to judge trial quality holistically from images.

Recommended outputs per trial:

```text
outputs/trials/<trial_id>/before.png
outputs/trials/<trial_id>/after.png
outputs/trials/<trial_id>/overlay.png
outputs/trials/<trial_id>/metrics.json
outputs/trials/<trial_id>/contact_sheet.jpg
outputs/trials/<trial_id>/notes.md
```

---

## 9. Suggested repo structure

A future implementation pass could set up the project roughly like this:

```text
sculptglmnop/
  README.md
  DEV_WORKFLOW.md
  docs/
    handoffs/
    boundary_brush_design.md
    testing_protocol.md
  src/
    boundary-brush/
      detectBoundary.js
      sampleBrushRegion.js
      smoothLabels.js
      applyVertexColors.js
      previewOverlay.js
  tests/
    unit/
    fixtures/
  tools/
    blender/
      generate_boundary_fixtures.py
      render_boundary_trials.py
      make_contact_sheet.py
  outputs/
    trials/
```

This structure separates the interactive WebGL/SculptGL work from the Blender evaluation harness.

---

## 10. First implementation milestone

The first milestone should be deliberately small:

```text
Milestone 1: Boundary Brush Prototype Harness

Goal:
Create a minimal reproducible pipeline where a jagged two-color mesh fixture can be generated, processed by a boundary cleanup function, and rendered before/after for visual review.

Acceptance criteria:
- one controlled mesh fixture exists
- colors/material labels are readable
- a local cleanup algorithm can be applied in script form
- before/after/contact-sheet images are generated
- no full UI integration required yet
```

Only after the algorithm/test loop is visible should the project wire the tool into SculptGL’s brush UI.

---

## 11. Second implementation milestone

```text
Milestone 2: SculptGL UI Integration

Goal:
Expose the boundary cleanup as an interactive brush inside the SculptGL fork.

Acceptance criteria:
- user can select boundary brush
- brush radius/strength visible and adjustable
- brush samples local two-color boundary under cursor
- brush modifies vertex/face color labels
- undo works or at least changes are isolated for rollback
- export preserves cleaned color data
```

This should not begin until the scripted fixture tests show the cleanup logic is worth integrating.

---

## 12. Design constraints and risks

Important constraints:

```text
Do not destroy source mesh geometry unless explicitly testing geometric smoothing.
Prefer color/material label reassignment over mesh deformation at first.
Preserve undoability.
Avoid global color changes when the brush is local.
Avoid turning two-color cleanup into uncontrolled smoothing of all colors.
Keep export compatibility in mind.
```

Risks:

```text
A boundary that looks smoother in viewport may slice worse if vertex interpolation changes unexpectedly.
Face-color and vertex-color workflows may need different algorithms.
Low-poly geometry may not support a truly smooth boundary without remeshing.
Aggressive cleanup can erase intentional small details.
SculptGL internals may make color data handling less direct than expected.
```

---

## 13. Suggested questions for the next chat or agent

A future chat should answer these before coding deeply:

```text
1. Does the SculptGL fork store color per vertex, per face, or through another attribute path?
2. What file formats must preserve the cleaned colors for Bambu or downstream slicers?
3. Should the first algorithm operate on face labels, vertex colors, or both?
4. What minimum fixture set proves the boundary brush is useful?
5. Can Blender generate the exact kind of vertex-color mesh Bambu Studio expects?
6. How should strength be defined: distance from boundary, number of graph iterations, curve fit tolerance, or label-confidence threshold?
7. What visual metric best catches “streaky lone lines breaking the flow”?
```

---

## 14. Recommended first prompt for a future coding agent

Use this only after reopening the SculptGL project in a dedicated chat.

```text
Date/Time: <current local date/time>
Job Title: SculptGL Boundary Brush Reboot — Fixture Harness First
Project/Repo: boglim1984/sculptglmnop
Version/Build: Boundary Brush Prototype v0.1
Source Chat URL: https://chatgpt.com/c/6a06d242-1d94-83ea-8c69-36f52b899e3e
Recommended Model: Codex 5.5 High
Model Reason: This requires codebase setup, mesh/color data inspection, Blender fixture generation, and repeatable visual testing.

Task:
Set up the first boundary-brush prototype harness for the SculptGL fork. Do not integrate the UI yet. Create controlled two-color jagged-boundary mesh fixtures, implement a script-level color-label cleanup prototype, and render before/after/contact-sheet outputs for visual review.

Hard constraints:
- Do not rewrite the SculptGL app architecture.
- Do not commit large generated outputs.
- Do not modify unrelated files.
- Protect secrets and do not commit local configs.
- Keep source vs generated output separate.

Acceptance criteria:
- fixture generator exists
- at least one jagged two-color triangular mesh fixture exists
- cleanup prototype runs from command line
- before/after/contact-sheet images are generated
- docs explain how to reproduce the trial
- next step recommends whether to proceed to UI integration
```

---

## 15. Final handoff note

This document intentionally preserves the project’s conceptual origin rather than pretending implementation already happened. The current chat became the main GCR registry/scheduler hardening thread, so SculptGL should continue in a fresh dedicated chat with this file as the restart point.

The shortest project identity is:

```text
A SculptGL fork for a local boundary brush that cleans jagged two-color vertex/material regions on triangulated meshes, tested through Blender-generated fixtures and visual contact sheets before UI integration.
```
