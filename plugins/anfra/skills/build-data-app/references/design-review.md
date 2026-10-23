# Reviewing a Data App's design

Two uses, one procedure:

- **Review job.** "Review", "critique" or "audit": report the findings and change nothing until the
  user picks. "Polish", "fix", "clean up", "make it look good": report, then fix every finding,
  Nits included, until the page meets the bar below.
- **Self-check:** step 5 of a build or change. Fix every Blocking and Should finding before you
  hand over, then report what you fixed. Review as a stranger would: the builder has seen the page
  only in success, in light, at one width, which is exactly where its flaws don't show.

**The bar is the build's.** A reviewed page meets the standard of a page the build steps would
write today from the same content: the look's tokens, the example's components, page-design.md's
layout, copy.md's words. Judge the page against that page, never against itself. Most apps under
review were written before these rules or from an agent's defaults: their own palette, their own
controls, a layout by inventory. Each part can pass a yes/no rule on its own while the page still
looks unfinished, so a review that fixes the wording and adds delta pills, and leaves the palette,
the browser-drawn selects and the layout as they were, has changed nothing the reader sees.

The rules behind each check are in [page-design.md](page-design.md) and [copy.md](copy.md). A
review never changes what a number means. When a number looks wrong (a rate over 100%, a total
that doesn't match its parts), report it as a data finding and leave the AQL for the user to
decide.

## 1. Set the look: the user's, never the app's

Settle the look before you read for findings, because it sets what every color, type and
component check compares against:

```
Did the user name a brand, colors or a reference, or say "keep our look"? ─▶ their own design
Did they ask for something creative, or does the app's header comment
record a look the user chose?                                             ─▶ that look
Otherwise                                                                 ─▶ the default anfra look
```

The app's current palette, fonts, component styles and theme switcher are what is under review,
never evidence of a chosen look: reading them as "their own design" is how a polish keeps every
color and control it should have replaced. Name the look in the report's first line ("Against the
default anfra look"), so the user can correct it in one reply.

- **Default look:** [example.html](example.html) is the target: its token block, its components,
  its chart theme. Every check applies.
- **Their own design:** their colors and type fill the same token names; the components' shape,
  height and surface, the layout recipes and every check still apply.
- **Creative:** the floors, the unslop pass and the story (checks 2 to 5) are findings; other
  departures from the baseline are choices, which you may offer as suggestions.

## 2. Read the code, write the inventory and the component map

**Read the app's file whole:** markup, styles, chart options and render code. Then write its
inventory, one line each:

- each block: its handle, the query it draws, its form (chart type and series count, a table,
  KPIs);
- each control: its handle, its kind, how many options it has, what it maps to;
- each chart click that filters the page.

Find each query's branches in the render code: no result yet, executing with a result, success
with rows, success with none, error. A branch missing from the code is a finding even when the
screenshot looks right, because a build only ever shows success.

**Then write the component map:** one line per kind of part the page has, what the app draws now,
and what it must be. Every line where the two differ is a finding (check 27), Should or higher,
because the reader sees it in the first view.

| Part | Must be (default look) |
| --- | --- |
| Tokens | The example's `:root` and `.dark` blocks, value for value: DESIGN.md's colors on Basecoat's variables, the shadows, the chart palette and ramps |
| Type | Inter from `@fontsource` as the example loads it; 20, 14 and 12px; weights 400, 500, 600 |
| Header | `.page-header`: the `h1`, one caption line, the page controls on the right of the same row |
| 2 to 5 options | `.segmented`, the pressed option a white chip |
| More than 5, one pick | Basecoat `div.select` on the example's control surface |
| Several picks | The example's one-line chip multi-select, with remove buttons and "+N" |
| Button | Basecoat `.btn` with `data-variant`: primary only for the page's one action, outline, ghost |
| Text or number field | Basecoat `.input`, 32px |
| Panel | `.card` with `data-size="sm"`, its header `h2` at the optical inset |
| KPI | `.card.kpi`: label, figure, delta pill |
| Chart | `chartTheme()` colors, `tooltipStyle()` with a `tooltipHtml()` formatter, `animation: false` |
| Table | page-design.md, Tables: 36px rows, hairline rules, right-aligned `tabular-nums` |
| Loading, empty, error | `.status` over the block: skeleton, then message in words |
| Footer | `.definitions`: a `dl` per measure in the KPI columns, then one basis line |
| A part the example lacks (a step list, a progress bar, a badge) | Basecoat's component for it if it has one, else built from the tokens: no color, size, radius or shadow that isn't a token |

Write it for this app, as "Now → Must be" with a count: "Selects: 9 native `<select>` → Basecoat
`div.select`". The count tells the fix how far the change reaches.

**Flag on sight.** Search the app's own `<style>` and `<script>` (not Basecoat's) for these. A hit
is a lead, not yet a finding: confirm it in context.

| Pattern | Instead | Check |
| --- | --- | --- |
| a `:root` without `--shadow-e1` and `--chart-cat-1`, or with values that differ from the example's (default look) | the example's token block | 20 |
| any `<select>` element | Basecoat `div.select`, or `.segmented` for 2 to 5 options | 27 |
| the app's own chip, pill, tag or toggle classes; `border-radius: 9999px` on something clickable | `.segmented`, `.btn`, or the chip multi-select | 27 |
| a `<button>` without `.btn`, or with its own background or border rules | a `.btn` variant | 27 |
| `font-family` other than the look's; `font-size` other than 20, 14 or 12px; `font-weight: 700` or `bold` | Inter, the three sizes, 600 | 14 |
| `text-transform: uppercase` | sentence case | 16 |
| `colorBy: 'data'`, an `itemStyle.color` function, or a `color` per data item on one series | the series' one color | 18 |
| two entries in `yAxis`; `type: 'pie'` with more than 3 data | aligned charts or a table; sorted bars | F1 |
| a `legend` on one series | none; end labels for 2 to 3 lines | 19 |
| chart options without `animation: false`; `IntersectionObserver` | `animation: false`; render at load | F7 |
| a hex, `rgb(`, `white` or `black` outside `:root`, `.dark` and `chartTheme()`; an ECharts `tooltip` without `tooltipStyle(theme)` and a `tooltipHtml()` formatter | a token; the one tooltip | 20 |
| `toFixed(`, `toLocaleString()` with no options, `+ '%'`, a hyphen as minus | the page's `Intl` formatters, `−` | F2 |
| `transition: all`; a transition on `width`, `height`, `top` or `margin` | name `opacity` or the color properties | 21 |
| `:hover` with `transform` or `box-shadow` on a card that does nothing on click | nothing | 1 |
| an emoji in the markup or in a label string | nothing, or one icon set | 1 |
| `outline: none` with no `:focus-visible` rule; a click listener on a `div` or `span`; an icon-only `<button>` without `aria-label` | the one focus ring; `<button type="button">`; `aria-label` | F5 |
| a control inside a `.card` that isn't that chart's own, in its header; a filter panel above the page | the header row | 11 |
| `100vh`, or `overflow: auto` on the page wrapper | nothing: the frame scrolls | 12 |
| a `span-N` class or `grid-column: span` on a direct child of the page, outside a `.row` | wrap it in a `.row` | 9 |

## 3. Draw the target page

Write the page plan (page-design.md, section 1) for this app as if you were building it today from
its content, then its target layout, one line per row, each block with its span and form, each
control with its place and component:

```
Header   "Funnel analysis", caption "Signed up in 2025 Q2, compared with 2025 Q1"
         controls on the right: Country (chip multi-select), Signed up (select), Conversion window (select, 6 options)
Row 1    Overall conversion, the lead, span-3 | Entered span-3 | Converted span-3 | Median time span-3
Row 2    Funnel by step, bars, span-8 | Steps, the step builder, span-4
Row 3    Conversion by segment span-6 | Conversion by signup cohort span-6
Row 4    Time to convert, columns, span-12
Footer   a definition under each KPI column, then the basis line
```

Use the anatomy and recipes in page-design.md, sections 2 and 3. Keep every feature a reader uses:
each control, block and interaction is moved and restyled, never dropped without the user's yes.
Drop only what the unslop pass deletes (decoration, filler, a greeting).

In a self-check, the target is the plan the user agreed to at step 2: don't write a second one.

Then compare the page with the target. Each block in the wrong row, order or span, each control in
the wrong place, each chart in the wrong form, and each hole (a column that ends while its
neighbor runs on, leaving bare paper beside it) is a layout finding (check 6).

## 4. Ask before you look

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

## 5. Walk the checks in order

**Floors first, in every look.** A "no" is Blocking, and no new layout fixes it. **Then the
unslop pass ([unslop.md](unslop.md)), also in every look:** each tell it finds is a finding
(Should, or Blocking when it misleads), never a suggestion, because a page that reads as
generated is not a style. Then the rest, in order: a page that leads with the wrong thing gets a
new layout, which makes its spacing findings moot. Section 1 says which of them a creative look
skips.

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
   emoji and icons on every label, single-series legends, decorative cards, cards that lift on
   hover)

**Story**
2. Does the first view (1166 × 834px) hold the header, the lead and the top of the main chart?
3. Is there one lead, first and on the left, with its comparison?
4. Does the page open on a period and comparison that show the finding, rather than all time?
5. Is any lead sentence computed from the data, claiming only what a query measured?

**Layout**
6. Does the page follow the target layout (section 3) row by row: each block in its row, order and
   span, each control in its place, no hole beside a column that ended early?
7. Are there 4 or fewer KPI tiles?
8. Does each chart get the span its content needs (a time series 6 columns or more, labeled bars
   4 or more), rather than a span forced to match the row above? Does the wider span go to the
   chart that explains the lead?
9. Is the space above each card's first line, to the top of its capitals, equal to the card's
   side padding (or, beside a control, centered on it)? Do titles in one row share one line? Do
   cards in a row share one height, and charts in a row one height? Does every card span whole
   columns of one 12-column grid, so edges between rows match exactly or differ by a full column,
   never by a few pixels?
10. Is every layout space (page padding, between and inside cards) on the 4, 8, 12, 16, 24, 32px
    scale, set by `gap`, with any other value commented?
11. Are page controls in the header row, or a bare row under it, and never in a card or a filter
    panel? Is each chart-only control in that chart's header?
12. Does the page hold at 760px with no horizontal scroll and no overlapping text?
13. Does the chart hold its worst row: the longest name truncated with an ellipsis, a null, a
    zero, a single category?

**Hierarchy and type**
14. Is the typeface the look's, in only 20, 14 and 12px, and weights 400, 500 and 600?
15. Is every finding, instruction and empty-state message 14px or more? Are numbers in
    `tabular-nums`, right-aligned in tables?
16. Is everything sentence case, with no uppercase eyebrows?

**Color, charts and surfaces**
17. In the UI, does the accent mark only focus, a selected option or a primary action?
18. Does a single series take one color, with a selection dimming the other marks rather than
    recoloring them? Are red and green kept out of marks, and used only in KPI delta pills beside
    an arrow and a sign? Are status colors kept out of series?
19. Does each chart match the chooser (no unsorted bars of unordered categories), with end labels
    that don't collide on 2 to 3 lines instead of a legend? Do series more than 5× apart, or in
    different units, get small multiples on a shared x-axis rather than one axis?
20. Does every color come from the look's tokens (in the default look, the example's token block,
    value for value; `chartTheme()` for ECharts; tooltips included), so the page reads as anfra
    and dark flips it? Do surfaces use the shadow tokens, one depth cue each, with no decorative
    gradient?
21. Do axes recede (12px muted labels, value-axis split lines only, round ticks)? Do all tooltips
    use `tooltipStyle()` and `tooltipHtml()`, so they look alike and stay inside the frame? Is a period still filling labeled "to date"? Is motion
    limited to hover, press and popovers, on named properties, with none on load?

**Copy and numbers**
22. Is the title 1 to 4 words, in the reader's words, without "Dashboard"?
23. Does every card heading say what is plotted and against what?
24. Is each concept named one way across KPI, chart, table and footer, with no field names?
25. Does the footer define each measure in its own entry (term over one clause, in its KPI's
    column), with the comparison, date basis and as-of date in one line, not a paragraph?
26. Do empty and error states name the scope or the block in words, inside the block?

**Components and controls**
27. Does every part match its line in the component map: no browser-drawn `<select>`, no chip,
    pill or toggle the app styled itself, no button outside the `.btn` variants, every panel a
    `.card`? A part drawn by the browser or by one-off CSS beside Basecoat's reads as a second
    library on the page, however well each looks alone.
28. Is each control the right kind for its option count (segmented for 2 to 5, a select past 5)?
    Do fields, selects, outline buttons and the pressed segment share one surface: white, raised
    by the soft `--shadow-e1` ring, never a hard border?
29. Do periods come as presets, and "Clear selection" show only while there is a selection?

## 6. Report

Before you report, re-read the code behind each finding, and drop anything the look allows, a
comment records as deliberate, or another row already covers. When one cause produces many
instances (nine native selects, a raw hex in every chart), report one row with the count, and fix
it once at its source.

A page that matches its component map and its target and passes every check is **Ready** with no
rows. Report nothing to fill the table: a nitpick made up to look thorough costs the reader's
trust in the real ones. Name each finding's place by its handle and its `file:line`.

Open with the look and the verdict in one line: "Against the default anfra look: **Blocking**" (a
floor fails), **Fix before sharing** (Should findings only), or **Ready**. Then the story sentence,
before and after. Then the target layout (section 3) and the component map's lines that differ.
Then one table, most severe first, each part named by its handle so the user can find it in the
Inspect panel, and each fix concrete enough to apply without asking:

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

## 7. Fix

A review that asks to fix, polish or clean up fixes right after the report: every finding, Nits
included, until the page matches its target and its component map. One that only asks for a
review waits for the user to pick. Fix in this order, each at its root, so a later step builds on
the earlier one instead of redoing it:

1. **Tokens and base.** Put the example's foundation in the app's `<head>`: the Inter and
   Basecoat links with their hashes, the theme script, the `:root` and `.dark` token blocks, and
   the rules for type, controls, cards, rows, segmented control, multi-select, status, tooltip and
   definitions. Delete the app's own palette, font stack and every rule these replace. Two systems
   side by side is how a polish leaves half the page in the old look. In their own design, their
   colors go into the same token names.
2. **Components.** Rewrite each control's markup as its component in the map, and point its
   existing listeners at the new elements. The state calls stay as they are: the same filter or
   `setValue` call fires on the new control's change.
3. **Layout.** Rebuild the page's markup on the target rows (`.row` and spans), moving each block
   with its `data-anfra-*` attributes.
4. **Charts.** The example's `chartTheme()`, `tooltipStyle()` and `tooltipHtml()`, colors from the palette, one
   color per single series, the chooser's form for each block.
5. **Copy, numbers and states**, with copy.md.
6. **The unslop pass**, last, on the result.

Never change the AQL, the SDK declarations or what a number means: a finding that needs a
different query goes back through build steps 2 and 3. Run `anfra validate` and quote its result.

**Done when** each of these holds; re-read the file for each, never tick one from memory:

- Every line of the component map reads "matches".
- `rg -n '<select' <app>` finds nothing (in the default look).
- No hex, `rgb(`, `white` or `black` outside the token blocks and `chartTheme()`, and the token
  blocks are the look's, value for value.
- The markup follows the target layout row by row, with no hole.
- No single series is colored per data point.
- Set the app beside example.html: a reader would say one team built both. Where they wouldn't,
  that part is still open.

Then look again (section 4) and walk only the checks the fixes touched. When a later request
names a part by its handle, search the file for that attribute.
