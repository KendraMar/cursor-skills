---
name: ux-audit
description: >-
  Audit a frontend prototype for UX defects and opportunities. Covers
  accessibility, interaction smoothness, workflow efficiency, corner case
  handling, and PatternFly compliance — mapped to NNG usability heuristics.
  Use when the user asks for a UX audit, UX review, accessibility check,
  interaction review, PatternFly compliance check, or prototype quality review.
---

# UX Audit (PatternFly + NNG Standard)

Systematically audit a frontend prototype for user experience defects and
improvement opportunities. Each finding is mapped to the corresponding
[NNG usability heuristic](https://www.nngroup.com/articles/ten-usability-heuristics/)
and produces a severity-rated report grouped by category.

## NNG Heuristic Reference

| # | Heuristic | Quick test |
|---|-----------|------------|
| 1 | Visibility of system status | Every async action has a loading/progress indicator |
| 2 | Match between system and real world | No technical jargon, DB keys, or raw IDs shown to users |
| 3 | User control and freedom | Cancel/Undo available; no dead-end states |
| 4 | Consistency and standards | Danger = red; labels don't mix "Remove"/"Delete"; PF patterns followed |
| 5 | Error prevention | Submit disabled on invalid form; client-side validation present |
| 6 | Recognition rather than recall | Options visible; no memorization required |
| 7 | Flexibility and efficiency of use | Shortcuts for power users; progressive disclosure |
| 8 | Aesthetic and minimalist design | No irrelevant info; clean hierarchy |
| 9 | Help users recognize, diagnose, recover from errors | Plain-language errors with actionable next steps |
| 10 | Help and documentation | Tooltips, hints, onboarding where needed |

## Execution Steps

1. **Context Discovery** — Identify the project root and tech stack
   (React + PatternFly focus). Scan `Layout`, `App.tsx`, and routing.
2. **Read all component files** — `.tsx`, `.jsx`, `.css`, and layout files.
3. **Run each audit category below** against the code, checking every item.
4. **Map each finding** to the corresponding NNG heuristic number.
5. **Compile findings** into the output format at the end.

---

## Audit Categories

### 1. Accessibility

*WCAG 2.1 AA level checks. Maps primarily to NNG #4 (Consistency) and #5 (Error Prevention).*

Check every interactive element for:

- **Keyboard access** — Can every action be reached and triggered via keyboard?
  Look for `onClick` on non-interactive elements (`div`, `span`) without
  `role`, `tabIndex`, or `onKeyDown`.
- **Interactive semantics** — `onClick` on `div` or `span` must be `Button`
  or `ActionListItem`. No fake links (`<a href="#">` with `preventDefault`).
- **ARIA roles and labels** — Do custom widgets have correct roles (`tab`,
  `tablist`, `menu`, `dialog`)? Do icons and image buttons have `aria-label`?
- **Focus management** — Does focus move logically after panel open/close,
  modal open, or drawer toggle? Is focus trapped in modals/drawers and
  returned to the trigger on close?
- **Color contrast** — Are status indicators conveyed by more than color alone
  (e.g., icon + color, not color-only)? Include hidden `ScreenReader` text
  where visual-only cues exist.
- **Screen reader content** — Are loading states, live regions (`aria-live`),
  and dynamic content changes announced? Are decorative images marked
  `aria-hidden`?
- **Form labeling** — Does every input have a visible label or `aria-label`?
  Every `FormControl` / `TextInput` must have a corresponding `FormGroup`
  with a label. Are required fields indicated both visually and
  programmatically?

Search patterns:
```
onClick.*<div    onClick.*<span    <img(?!.*aria)(?!.*alt=)
role=    aria-label=    tabIndex    onKeyDown    onKeyUp
href="#"    preventDefault
```

### 2. Interaction Smoothness & Performance

*Maps to NNG #1 (Visibility of system status) and #9 (Error recovery).*

Check for:

- **Loading feedback** — Is there a spinner, skeleton, or progress indicator
  for every async operation (data fetch, save, create, delete, file upload)?
  Check for `Skeleton` component usage during initial data loads.
  *(NNG #1)*
- **Optimistic updates** — Do destructive or slow operations update the UI
  immediately and roll back on failure? Or does the UI freeze while waiting?
  *(NNG #1)*
- **Error recovery** — After a failed save/create/delete, can the user retry
  without losing their input? Is the error message actionable and in plain
  language? *(NNG #9)*
- **Debounce and throttle** — Are search inputs, resize handlers, and scroll
  listeners debounced? Rapid typing in search should not fire per-keystroke
  API calls. *(NNG #1)*
- **Transition and animation** — Do panels, drawers, and dropdowns animate
  open/close? Are there jarring layout shifts when content loads? Check for
  images without dimensions or late-loading sidebars that cause page jumps.
- **Double-submit prevention** — Are submit buttons disabled while an operation
  is in flight? Can rapid clicks cause duplicate creates? *(NNG #5)*
- **Stale data** — After a successful mutation, does the UI reflect the change
  everywhere (list, detail panel, counts, filters)? *(NNG #1)*

### 3. Workflow Efficiency

*Maps to NNG #3 (User control), #6 (Recognition over recall), and #7 (Flexibility).*

Look for opportunities to:

- **Eliminate steps** — Can any two-step action become one? (e.g., click to
  edit + explicit save could be inline edit; separate filter + sort could be a
  single column header interaction) *(NNG #7)*
- **Reduce context switches** — Does the user need to leave the current view
  to perform a related action? Can it be done inline? *(NNG #6)*
- **Batch operations** — Can the user act on multiple items at once (multi-
  select, bulk status change, bulk move)? *(NNG #7)*
- **Smart defaults** — Are form fields pre-populated with sensible defaults
  based on context (e.g., current sprint, logged-in user as reporter)?
  *(NNG #6)*
- **Undo over confirmation** — Are there destructive-action confirmation
  dialogs that could be replaced with undo? (Undo is faster and less
  disruptive.) *(NNG #3)*
- **Progressive disclosure** — Is the UI showing too much at once? Can
  advanced options be hidden until needed? *(NNG #8)*
- **Shortcut opportunities** — Are there keyboard shortcuts for frequent
  actions (refresh, search focus, next/previous item)? *(NNG #7)*

### 4. Corner Case Handling

*Maps to NNG #5 (Error prevention) and #9 (Error recovery).*

Check every data-dependent view for:

- **Empty states** — What does each list, table, and section show when there
  are zero items? Is there a helpful message and a call to action? Must use
  PF `EmptyState` with `EmptyStateBody` and a primary action. *(NNG #9)*
- **Error states** — What happens when an API call fails? Is there a visible
  error with retry option, or does the UI silently show stale/empty data?
  *(NNG #9)*
- **Null and missing data** — For every field displayed, what happens if the
  API returns `null`, `undefined`, or an empty string? Will the UI crash, show
  "undefined", or degrade gracefully? *(NNG #5)*
- **Long content** — What happens with very long titles, descriptions, or
  names? Truncation with tooltip? Overflow? Layout break?
- **Concurrent operations** — What if the user drags an item while a save is
  in progress? Opens a panel while auto-refresh fires? Creates while another
  tab creates? *(NNG #5)*
- **Network failure** — Does the app handle offline, slow, or intermittent
  connectivity? Are there timeouts? *(NNG #9)*
- **Boundary values** — Zero story points, past due dates, future-dated
  comments, max-length input fields. *(NNG #5)*

Search patterns:
```
catch    .catch    try {    finally    ?? ""    || ""
dangerouslySetInnerHTML    .toLowerCase()    .split(
```

### 5. PatternFly Compliance

*Maps to NNG #4 (Consistency and standards).*

Check for deviations from PatternFly's component library:

- **Custom components replacing PF equivalents** — Is there a hand-built
  dropdown, tab bar, modal, or tooltip when a PatternFly component exists?
  Look for custom `div` + `onClick` tabs, custom select dropdowns, custom
  modals. *(NNG #4)*
- **IA and navigation** — Does every non-dashboard page have a `Breadcrumb`?
  Is the `Page` component using `PageSidebar` and `PageHeader` correctly?
- **CSS overrides** — Count `!important` declarations. Are they overriding PF
  tokens? Could the same result be achieved with PF props, variants, or CSS
  variables?
- **Incorrect component usage** — Are PF components used with the wrong
  variant, size, or prop combination? (e.g., `Button` as a link without
  `component="a"`, `Label` without appropriate color mapping) *(NNG #4)*
- **Hardcoded values** — Are colors, spacing, or font sizes hardcoded instead
  of using PF CSS variables (`--pf-t--global--*`)?
- **Layout** — Is the page using PF `Page`, `PageSection`, `Flex`, `Grid`,
  `Gallery`? Or raw `div` with inline styles for layout?
- **Icons** — Are icons from `@patternfly/react-icons`, or are there inline
  SVGs or other icon libraries?
- **Responsive behavior** — Are PF responsive props used (`display`,
  `visibility`, breakpoint props), or is responsive behavior missing entirely?
- **Empty state quality** — Lists/tables must use PF `EmptyState`. Flag any
  empty state missing `EmptyStateBody` or a primary action.

Search patterns:
```
!important    style={{    style=    <div style
var(--pf    @patternfly    hardcoded hex (#[0-9a-f]{3,6})
```

---

## Output Format

Present findings in this structure:

```
## UX Audit Report — [Project Name]

### Summary
- Critical: [count]
- High: [count]
- Medium: [count]
- Low: [count]
- Opportunities: [count]

### Critical
[Items that break functionality or block users]

### High
[Items that significantly degrade the experience]

### Medium
[Items that cause friction but have workarounds]

### Low
[Polish items and minor inconsistencies]

### Opportunities
[Not defects — ideas for improving the experience]
```

Each finding should include:

| Field | Content |
|-------|---------|
| **Category** | Accessibility / Interaction / Workflow / Corner Case / PatternFly |
| **NNG Heuristic** | Number and name (e.g., "#1 Visibility of system status") |
| **Location** | File path and line range or component name |
| **Issue** | What is wrong or what could be better |
| **Impact** | Who is affected and how |
| **Recommendation** | Specific fix or improvement |

---

## Severity Definitions

| Severity | Definition |
|----------|------------|
| **Critical** | Users cannot complete a core task; app crashes; security/XSS |
| **High** | Significant UX degradation; key workflow blocked for some users; data loss risk |
| **Medium** | Noticeable friction; workaround exists; affects minority of users |
| **Low** | Polish; minor inconsistency; affects edge cases only |
| **Opportunity** | Not a defect — a chance to meaningfully improve the experience |

---

## Additional Resources

- For NNG heuristics, see [10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/)
- For PatternFly component API details, see [PatternFly docs](https://www.patternfly.org/)
- For WCAG 2.1 AA criteria, see [WCAG quick reference](https://www.w3.org/WAI/WCAG21/quickref/)
