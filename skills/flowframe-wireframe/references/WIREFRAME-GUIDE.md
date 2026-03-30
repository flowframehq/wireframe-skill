# Wireframe Guide

This guide is the primary reference for composing FlowFrame wireframes.
Use it to decide screen shape, split regions, control width and density, and expose the states a human reviewer must understand.

## Table of Contents

- [Step 1: Choose a Layout](#step-1-choose-a-layout)
- [Step 2: Split the Regions](#step-2-split-the-regions)
- [Step 3: Refine the Balance](#step-3-refine-the-balance)
- [Key Patterns](#key-patterns)

## Step 1: Choose a Layout

Start from the screen's dominant job, not from visual decoration.

| Screen shape | Use when | Recommended structure |
|-------------|----------|-----------------------|
| Single-focus | One primary task or one leaf feature dominates the screen | Centered card or simple single-column section |
| Two-region | One supporting area controls one main area | Header + filter/sidebar + main content |
| Multi-region | Several independent responsibilities must stay visible together | Top bar + main workspace + one or more side/bottom panels |

Choose the simplest shape that can still show the feature relationships clearly.
Do not add extra columns or panels unless the screen would become ambiguous without them.

### Quick heuristics

- If the screen has one main CTA and one primary form, prefer a single-focus layout.
- If one region changes what another region shows, prefer a two-region layout.
- If users must compare or monitor multiple responsibilities at once, use a multi-region layout.
- If mobile would collapse the structure completely, split the viewport into a separate wireframe file instead of forcing one dense layout.

### Width baseline

- PC wireframes should keep the main content within roughly `1200px`.
- Mobile wireframes should treat roughly `370px` as the stable content width target.
- In PC layouts, keep the main working area visually dominant even when side panels exist.
- In mobile layouts, avoid forcing desktop-style multi-column composition into one narrow frame.

## Step 2: Split the Regions

After choosing the shape, separate the screen into fixed chrome and feature regions.

### Fixed chrome

Use fixed chrome for elements that provide frame or context but are not the main planning target.

Examples:
- top headers
- bottom status bars
- navigation shells
- simple branding or utility areas

Rules:
- fixed chrome has no `data-feature`
- fixed chrome does not appear in metadata `elements`
- fixed chrome should stay visually weaker than the main feature area

### Feature regions

Use feature regions for referenced leaf specs.

Rules:
- each referenced leaf needs a clear visual home in the layout
- if two leaves belong in the same area, stack them in one plain wrapper and put `data-feature` on the tracked inner elements
- if one leaf would need to appear in two distant places, the spec boundary is probably wrong and should be split upstream

### Region selection checklist

- What is the user's main working area?
- What controls or filters change that main area?
- What supporting information must stay visible while the main task happens?
- Which areas are just frame and should remain untracked?

### Overlay regions

Some feature types should not be treated as permanent sidebars or full regions.

#### Dialog / modal

Use a dialog or modal when the user must confirm, review, or complete a focused step before returning to the main screen.

Rules:
- keep the main screen visible underneath as background context
- show one representative open state rather than multiple stacked overlays
- use the overlay body for the tracked feature elements
- keep destructive or final CTA meaning explicit inside the overlay

#### Drawer

Use a drawer when supporting detail or secondary actions slide in while preserving awareness of the main screen.

Rules:
- keep the base screen visible and readable behind or beside the drawer
- anchor the drawer from one side and make it visually distinct from a permanent sidebar
- use a drawer when the panel is conditional or temporary, not always-on chrome
- if the drawer contains the main task, it is probably not a drawer and should be modeled as a primary region instead

### Switching regions

Use a tab pattern when multiple related feature views share the same space and only one should be visible at a time.

Rules:
- keep one stable tab header row
- show the active tab panel only
- make the active tab obvious through text weight, underline, or subtle contrast
- if each tab needs a completely different screen structure, split the screen or the feature instead of hiding everything behind tabs

## Step 3: Refine the Balance

Once the regions exist, adjust width, spacing, hierarchy, and state visibility so the wireframe is easy to review.

### Visual principles

1. Let content lead. Headers, filters, and helper panels should feel secondary to the main task.
2. Build hierarchy with typography, spacing, and neutral contrast instead of decoration.
3. Keep density intentional. Avoid both cramped grouping and large dead space.
4. Remove decorative noise. Strong shadows, gradients, or visual flourish weaken wireframe clarity.
5. Show the state that matters. If a reviewer would misunderstand the behavior without seeing a case, surface that case.

### Visual rules

- Use `text-sm` as the default text size.
- Use `text-lg` or `text-xl` for headings and keep heading weight at `font-semibold`.
- Keep UI surfaces boxy and simple: cards, panels, inputs, and buttons should read as structure, not finished branding.
- Include `dark:` variants for all color-related classes.

### Spacing and density

- Use `gap-2` or `gap-3` inside tight control groups.
- Use `p-4` for most cards and `p-4` or `p-6` for panels.
- Use `gap-6` or more between major sections.
- Reduce oversized placeholders or excessive vertical whitespace before adding more decorative framing.
- Keep PC layouts comfortably reviewable without stretching the main content past the `1200px` baseline.
- Keep mobile layouts readable within the `370px` baseline rather than shrinking every control to fit more columns.

### Pattern-specific notes

#### Search + filter + list/grid

- Keep the search and filter controls easy to scan in one pass.
- Keep filter areas visually narrower than the main results area.
- Make the list or grid the visual center of the screen.

#### Card lists

- Keep image placeholders from overpowering the information below.
- Keep repeated card text to roughly 3 to 4 lines of visible information.
- Multiple actions may exist, but only one CTA should feel primary.

#### Forms

- Keep field rhythm consistent.
- Keep helper links weaker than the primary CTA.
- Show one representative validation, disabled, or processing state when it affects the next action.

### State and helper placement

If a reviewer would misunderstand the feature without a visible case, show one representative case in the layout.

Good placements:
- directly under the affected control
- at the top of a feature block as helper or warning text
- in a compact `상태 예시` or `검토 포인트` box near the relevant region

Good cases to surface:
- empty results
- disabled CTA
- validation or warning copy
- confirmation context
- loading or permission cues
- open dialog or drawer state when that state changes the user's next action

### Common layout fixes

| Problem | Adjustment |
|--------|------------|
| The screen feels empty | tighten vertical space, reduce oversized placeholders, group related controls |
| The screen feels crowded | increase grouping gaps, simplify repeated cards, remove nonessential chrome |
| The wrong area draws attention | reduce contrast in secondary panels and strengthen the main content block |
| State is unclear | add one visible helper block, badge, or disabled control near the affected feature |

## Key Patterns

- Choose layout by responsibility shape, not by visual habit.
- Keep fixed chrome untracked and visually secondary.
- Give every referenced leaf a clear region in the screen.
- Show one representative state when behavior would otherwise be ambiguous.
- Prefer the simplest layout that still explains how the screen works.
