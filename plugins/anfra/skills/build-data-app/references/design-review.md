# Reviewing a Data App's design

Two uses, one procedure:

- **Review job:** the user asks to review, critique, audit or polish an app's design. Report
  findings, change nothing until they say which to fix.
- **Self-check:** step 5 of a build or change. Fix every Blocking and Should finding before you
  hand over, then report what you fixed. Review as a stranger would: the builder has seen the page
  only in success, in light, at one width, which is exactly where its flaws don't show.

The rules behind each check are in [page-design.md](page-design.md) and [copy.md](copy.md). A
review judges the page against them; it does not change what a number means. When a number looks
wrong (a rate over 100%, a total that doesn't match its parts), report it as a data finding and
leave the AQL for the user to decide.

## 1. Code first, then ask to look

**Read the code first, always.** Read the app's file whole: markup, styles, chart options and
render code. Then write its inventory, one line each:

- each block: its handle, the query it draws, its form (chart type and series count, a table,
  KPIs);
- each control: its handle, its kind, how many options it has, what it maps to;
- each chart click that filters the page.

Walk the checks (section 2) against this list: the chooser against each form, the control rule
against each option count. Then find each query's branches in the render code: no result yet,
executing with a result, success with rows, success with none, error. A branch missing from the
code is a finding even when the screenshot looks right, because a build only ever shows success.

**Flag on sight.** Search the app's own `<style>` and `<script>` (not Basecoat's) for these. A hit
is a lead, not yet a finding: confirm it in context.

| Pattern | Instead | Check |
| --- | --- | --- |
| `font-size` other than 20, 14 or 12px; `font-weight: 700` or `bold` | the three sizes; 600 | 13 |
| `text-transform: uppercase` | sentence case | 15 |
| two entries in `yAxis`; `type: 'pie'` with more than 3 data | aligned charts or a table; sorted bars | F1 |
| a `legend` on one series | none; end labels for 2 to 3 lines | 18 |
| chart options without `animation: false`; `IntersectionObserver` | `animation: false`; render at load | F7 |
| a hex, `rgb(`, `white` or `black` outside `:root`, `.dark` and `chartTheme()`; an ECharts `tooltip` without `backgroundColor` | a token | 19 |
| `toFixed(`, `toLocaleString()` with no options, `+ '%'`, a hyphen as minus | the page's `Intl` formatters, `−` | F2 |
| `transition: all`; a transition on `width`, `height`, `top` or `margin` | name `opacity` or the color properties | 20 |
| `:hover` with `transform` or `box-shadow` on a card that does nothing on click | nothing | 1 |
| `outline: none` with no `:focus-visible` rule; a click listener on a `div` or `span`; an icon-only `<button>` without `aria-label` | the one focus ring; `<button type="button">`; `aria-label` | F5 |
| `<select multiple>`; radio inputs for options | a segmented control or Basecoat select | 26 |
| a control inside a `.card` that isn't that chart's own, in its header | the header row | 10 |
| `100vh`, or `overflow: auto` on the page wrapper | nothing: the frame scrolls | 11 |
| a `span-N` class or `grid-column: span` on a direct child of the page, outside a `.row` | wrap it in a `.row` | 8 |

**Ask before you open a browser.** Opening a browser, driving agent-browser, or taking
screenshots starts processes and windows on the user's machine, so ask once, in one line, before
the first: "Can I open the app in a browser and take screenshots to check what the code can't
show?" Skip the question if the user already allowed it in this session.

- **Yes:** open the app in `anfra serve` (SKILL.md, step 5). Look at it in light and in dark, at
  a 1166px frame (a 1440 laptop) and at 760px (Inspect open), first the top 834px, then the full
  height. Exercise every control once with the mouse, then once with the keyboard alone: Tab
  reaches each control in reading order and shows its ring, Enter or Space opens it, Escape closes
  it and puts focus back on the control that opened it. Reach the empty state by filtering to a
  scope with no rows; never edit the app to force an error, read that branch in the code.
- **No, or no browser tool:** finish from the code. List the checks you could not settle in the
  report, and offer to settle them from screenshots the user sends. Never report a check only eyes
  can settle (what fits in the first view, overlaps at 760px, equal card heights) as passed.

**The story check.** In one sentence: what the page tells its reader first (from the screenshot,
or from the markup's order and the lead's code), and what it should tell first, from the request
or the page title. If they differ, the first finding is the story.

## 2. Walk the checks in order

**Floors first, in every look.** A "no" is Blocking, and no new layout fixes it. **Then the
unslop pass ([unslop.md](unslop.md)), also in every look:** each tell it finds is a finding
(Should, or Blocking when it misleads), never a suggestion, because a page that reads as
generated is not a style. Then, in the default look, walk the rest in order: a page that leads
with the wrong thing gets a new layout, which makes its spacing findings moot. In the user's own
design or a creative look, walk only the story (2 to 5) after those; offer other departures from
the baseline as suggestions.

**Floors**
- F1. Do bars start at 0, with no dual y-axis and no pie past 3 parts?
- F2. Does every number carry its unit, and every change its comparison, in one precision per
  measure?
- F3. Does each query have its loading, updating, empty and error branch, each in its block's
  loaded size? Is an old figure never shown undimmed under a new scope's label? Do no-access and a
  lost connection read differently from a failure?
- F4. Does text pass 4.5:1, and do chart marks pass 3:1, in light and dark? Settle it
  from the tokens, not by eye: list each text color with each surface it sits on (page, card,
  sunken track, chip, tooltip) and compute the ratio. To fix a pair, move the foreground's `oklch`
  lightness away from the surface's and leave hue and chroma alone.
- F5. Is every control a real button or input, with a visible label, the one focus ring, a name if
  it is an icon, and the same height as its neighbors? Does a multi-select stay one line, with a
  remove button on each chip?
- F6. Can every colored mark be read without hover (a label, an end label, a value in a table)?
  Pictures and keyboards never see a tooltip. Does a chart click that filters the page also have a
  control for the same field?
- F7. Is the page picture-safe: `animation: false` on every chart, and nothing that waits for a
  scroll or a hover to appear?

**Subtract**
1. Does every element survive "what breaks if this is gone"? (greetings, restating captions,
   icons on every label, single-series legends, decorative cards, cards that lift on hover)

**Story**
2. Does the first view (1166 × 834px) hold the header, the lead and the top of the main chart?
3. Is there one lead, first and on the left, with its comparison?
4. Does the page open on a period and comparison that show the finding, rather than all time?
5. Is any lead sentence computed from the data, claiming only what a query measured?

**Layout**
6. Are there 4 or fewer KPI tiles?
7. Does each chart get the span its content needs (a time series 6 columns or more, labeled bars
   4 or more), rather than a span forced to match the row above? Does the wider span go to the
   chart that explains the lead?
8. Is the space above each card's first line, to the top of its capitals, equal to the card's
   side padding (or, beside a control, centered on it)? Do titles in one row share one line? Do cards in a row share one height, and charts in a row one height? Does every card span
   whole columns of one 12-column grid, so edges between rows match exactly or differ by a full
   column, never by a few pixels?
9. Is every layout space (page padding, between and inside cards) on the 4, 8, 12, 16, 24, 32px
   scale, set by `gap`, with any other value commented?
10. Are page controls in the header row, or a bare row under it, and never in a card? Is each
    chart-only control in that chart's header?
11. Does the page hold at 760px with no horizontal scroll and no overlapping text?
12. Does the chart hold its worst row: the longest name truncated with an ellipsis, a null, a
    zero, a single category?

**Hierarchy and type**
13. Are there only 20, 14 and 12px sizes, and weights 400, 500 and 600?
14. Is every finding, instruction and empty-state message 14px or more? Are numbers in
    `tabular-nums`, right-aligned in tables?
15. Is everything sentence case, with no uppercase eyebrows?

**Color, charts and surfaces**
16. In the UI, does the accent mark only focus, a selected option or a primary action?
17. Are red and green kept out of marks, and used only in KPI delta pills beside an arrow and a
    sign? Are status colors kept out of series?
18. Does each chart match the chooser (no unsorted bars of unordered categories), with end labels
    that don't collide on 2 to 3 lines instead of a legend? Do series more than 5× apart, or in
    different units, get small multiples on a shared x-axis rather than one axis?
19. Does every color come from a token (CSS variable, or `chartTheme()` for ECharts), tooltips
    included, so dark flips it? Do surfaces use the shadow tokens, one depth cue each, with no
    decorative gradient?
20. Do axes recede (12px muted labels, value-axis split lines only, round ticks)? Do tooltips set
    `confine`? Is a period still filling labeled "to date"? Is motion limited to hover, press and
    popovers, on named properties, with none on load?

**Copy and numbers**
21. Is the title 1 to 4 words, in the reader's words, without "Dashboard"?
22. Does every card heading say what is plotted and against what?
23. Is each concept named one way across KPI, chart, table and footer, with no field names?
24. Does the footer define each measure in its own entry (term over one clause, in its KPI's
    column), with the comparison, date basis and as-of date in one line, not a paragraph?
25. Do empty and error states name the scope or the block in words, inside the block?

**Controls**
26. Is each control the right kind for its option count (segmented for 2 to 5, a select past 5)?
    Do fields, selects, outline buttons and the pressed segment share one surface: white, raised
    by the soft `--shadow-e1` ring, never a hard border?
27. Do periods come as presets, and "Clear selection" show only while there is a selection?

## 3. Report

Before you report, re-read the code behind each finding, and drop anything the look allows, a
comment records as deliberate, or another row already covers. When one cause produces many
instances (a 13px size in nine rules, a raw hex in every chart), report one row with the count,
and fix it once at its source.

Report nothing to fill the table: a page that passes is **Ready** with no rows, and a nitpick
made up to look thorough costs the reader's trust in the real ones. Name each finding's place by
its handle and its `file:line`.

Open with one line: **Blocking** (a floor fails), **Fix before sharing** (Should findings only),
or **Ready**. Then the story sentence, before and after the fixes you propose. Then one table,
most severe first, each part named by its handle so the user can find it in the Inspect panel, and
each fix concrete enough to apply without asking:

| # | Where | Severity | Before | After | Why |
| --- | --- | --- | --- | --- | --- |
| 1 | `[Revenue by region](data-anfra-block="by-region")` | Should | Vertical bars in dataset order, legend "revenue" | Horizontal bars sorted by revenue, value at each bar's end, no legend | Unordered categories are compared by length; a sorted list reads at a glance |

Severity, first match wins:

```
Does it mislead: a wrong or unlabeled number, an old figure under a new label, a dishonest chart? → Blocking
Does it shut a reader out: contrast, no keyboard path, color alone, unreadable in dark?           → Blocking
Does it break: a control that does nothing, a clipped dropdown, a blank picture?                 → Blocking
Does the reader see it in the first view, in light, at 1166px?                                    → Should
Does it show only in dark, at 760px, or in a loading, empty or error state?                      → Should
Otherwise                                                                                         → Nit
```

## 4. Fix

In a review job that asks to fix, polish or clean up, fix right after the report: every Blocking
and Should finding and every slop tell. One that only asks for a review waits for the user to
pick findings. Fix Blocking findings first. Then fix at the
root: a token or a shared class that clears several rows at once; change the page's existing
tokens, never add a second scale beside them. Then the rest, grouped by part, the smallest change
each, keeping the SDK wiring as it is unless a finding is about it. Run `anfra validate` and quote
its result. Look again (section 1) and walk only the checks the fixes touched. When a later request
names a part by its handle, search the file for that attribute.
