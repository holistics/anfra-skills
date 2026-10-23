# Designing a Data App page

Read this at step 2, before the declaration list, for every app in every look. How strictly it
applies depends on the look the user chose:

- **Default anfra style:** every rule below applies.
- **Their own design:** theirs wins wherever it speaks; these rules fill the gaps.
- **Creative:** this is the baseline to start from, not a rule set. Go as far as the user wants
  (layout, type, palette, density, chart style) and depart from any rule here on purpose. Only
  the floors in section 11 hold.

Words and numbers are in [copy.md](copy.md).

The canvas is the frame `anfra serve` gives the app: a scrolling document about **1166 × 834px on a
1440 × 900 laptop**, 760px wide when the Inspect panel is open, 1646px on a 1920 screen. The host
draws its own sidebar and header around it, and the frame is white until you paint it. A reader
scans the first view before deciding to read on, so the first view has to answer the question the
page was built for.
Agents left to their defaults lay a page out by inventory, one equal card per measure, and the
reader cannot tell what matters. Every rule below exists to give the page a top.

## 1. Plan the page before any markup

Write this plan and show it with the declaration list (step 2). Each line is one decision; none
may be left as "TBD". Where you had to guess, write the guess and mark it "(assumed)": the user
corrects a written guess in one reply, where an open question costs a round.

1. **Reader, moment and decision.** Who opens the page, when, and what they do after reading it,
   in one line: "GMs, at the Monday meeting: which hotel needs a rate or sales push this week".
   The moment sets the default period (a Monday meeting reads the last full week). A page with
   no decision is a report: say what it is for instead ("the monthly numbers the board sees").
   Name what the page replaces (a spreadsheet, a weekly email, a dashboard nobody opens): the
   page has to beat it at the reader's moment, or it won't be opened.
2. **The lead.** The one number the decision hinges on, and what it is compared with: "RevPAR,
   this year to date vs the same dates last year". When the request lists several measures, the
   lead is the one the decision turns on; the others become the rest of the KPI row.
3. **Blocks and controls in reading order.** For each block: the question it answers, its form
   (from the chart chooser below), the query that draws it. For each control: the question it
   lets the reader ask ("only my region?"). A control no reader question needs is left out.
4. **First view, on request, never.** First view is what fits in the first 1166 × 834px: the
   header, the lead, and the top of the main chart. On request is what sits behind a control or
   below the fold (a detail table, a second breakdown). Never is what the dataset has but this
   reader does not need; leave it out rather than adding a card for it.
5. **Defaults that show the finding.** The period, comparison and selection the page opens on.
   Open on the window where the story is (the last full month against the same month last year),
   never on "all time" with no comparison: a page that opens neutral makes the reader do the
   analysis.

## 2. Page anatomy

Top to bottom, in this order. Leave a part out when the plan has nothing for it; never reorder.

| Part | Holds | Rules |
| --- | --- | --- |
| Header | `h1` page name, one caption line with the scope and basis ("Year to date, compared with the same dates last year"); page-wide controls on the right of the same row | The host header already shows `<title>`; the `h1` stays because pictures and other hosts don't show the host header. No logo, greeting or user name: on `anfra serve` the user is the machine's account |
| Lead | A KPI row of 2 to 4 figures, or a hero: one figure beside one sentence, when one finding dominates | The lead figure comes first, on the left |
| Main | The chart that explains the lead: its trend, or where it comes from | Full width, or beside its breakdown at the spans the content needs |
| Detail | A table or secondary charts for the reader who keeps going | Below the fold is fine |
| Footer | Definitions of every measure on the page, the date basis, the data's as-of date | A definition list on the page grid, then one basis line: never a paragraph (below) |

- **KPI row or hero:**

  ```
  Does one number decide what the reader does, and can a query state why in one sentence?
  ├── Yes ─▶ hero: that figure beside its computed sentence (copy.md)
  └── No
      ├── 2 to 4 measures the reader compares ─▶ KPI row, the lead tile first
      └── more than 4 ─▶ the lead tiles in a row, the rest in a table
  ```
- **No more than 4 KPI tiles.** A fifth tile means the plan has no lead; move the extras into a
  table. Four is what a reader compares at a glance, and equal tiles in a long row have no top.
- **KPI tile:** label (14px, weight 500, weak ink), value (20px, weight 600, ink, `nowrap`), then
  the change: a pill holding an arrow and the signed change, followed by its comparison in muted
  12px text, "[↑ +6.2%] vs last year". The pill's tint comes from the measure's polarity: green
  when this direction is good for it, red when bad, gray when neither (a ticket count, a mix
  share). The arrow and sign carry the direction, so the meaning never rests on color. Pills only
  in KPI tiles: in tables and chart labels a change stays signed text, because a pill on every
  number marks none of them. No icon, no sparkline unless the trend is the point and no chart
  below shows it.
- **Page-wide controls sit in the header row**, right-aligned, at most three. More than three go
  in a bare row directly under the header. Never put them in a `.card`: a card reads as content,
  and Basecoat's `.card` clips a combobox dropdown at its bottom edge.
- **A control that changes one chart sits in that chart's card header**, on the right: a grain
  switch in the trend card, a metric switch in the breakdown card.
- **Definitions are a list, never a paragraph.** One `<dl>` entry per measure: the term (12px,
  weight 500, weak ink) over one clause of at most 15 words (12px, muted), in the column of its
  KPI tile on the 12-column grid and inset by the card padding, so its text lines up with the
  label of the figure it defines. Measures beyond the
  KPI row take the next columns. Then one basis line across the row: the comparison, the date
  basis, the as-of date. A run of definitions in one 12px paragraph wraps at a width unrelated to
  the cards above and makes the reader read all of it to find one measure.

## 3. Layout recipes

Use only these values. A value outside them gets a CSS comment saying why; a review treats an
uncommented one as a finding.

- **Page:** `max-width: 1280px; margin: 0 auto; padding: 24px; display: grid; gap: 16px`. Paint
  `html` and `body` with the page background, since the frame is white and pictures fall back to
  white. Never make the page a `height: 100vh` shell with inner scrollers: the frame already
  scrolls, and a picture of the app clips at the inner scroller.
- **Spacing:** layout spacing (the page padding, between cards, inside cards) is 4, 8, 12, 16,
  24 or 32px: 16px between cards and inside them, 24px above a new section, 8px between a label
  and its control. Insets inside a component (a chip's padding, a segmented track's inset) may use
  2, 6 or 10px. Use `gap` on the parent, never margins on children: a margin leaves a trailing
  gap and leaks out of the component.
- **One 12-column grid for every row, so edges never miss by a few pixels.** Each row is
  `grid-template-columns: repeat(12, minmax(0, 1fr)); gap: 16px`, and every card spans whole
  columns. Two rows then either share an edge exactly or differ by a full column, which reads as
  a choice. Rows built on their own tracks miss each other by a sliver: three equal KPI tiles
  over a `2fr 1fr` row are off by a third of the gap (about 5px), a jag the eye catches at once.
- **Spans work only inside a row.** The page itself is a one-column grid; every card sits in a
  `.row`, and a full-width card is a `.row` holding one `.span-12` card (or a plain child of the
  page with no span). A `grid-column: span 12` placed straight in the page makes the browser add
  11 columns and line every other section up side by side, the header and KPI row included.
  The example scopes its span classes to `.row >` so this can't happen; keep them scoped.
- **Spans follow the content, not the row above.** A chart needs room for its marks and labels:
  a time series at least 6 columns, a bar chart with category labels at least 4 (about 360px in a
  1166px frame), a table at least 6. Match the KPI tiles' edges when the content fits in them;
  when it doesn't, give the content the span it needs. Squeezing ten labeled bars into 3 columns
  to line up with a tile truncates every label and leaves the bars a sliver.

  | KPI tiles | Tile span | Main row (chart + companion) |
  | --- | --- | --- |
  | 2 | 6 | 12, 8 + 4, or 6 + 6 |
  | 3 | 4 | 8 + 4, or 6 + 6 |
  | 4 | 3 | 6 + 6, or 8 + 4; 9 + 3 only for a companion of a few short rows |

  Below 900px frame width every card spans 12; below 720px KPI tiles span 6.
- **Width says importance.** The chart that explains the lead takes the wider span and its
  companion the narrower (8 + 4). Equal spans (6 + 6) when both carry the story, or when the
  companion needs the room: a breakdown of ten labeled countries beside a weekly trend.
- **Equal heights:** cards in one row stretch to the tallest (`align-items: stretch`), and the
  charts in a row share one height: 320px for a main chart, 240px for a chart in a 3-up row.
  Ragged card bottoms read as unfinished.
- **Breakpoints follow the frame**, which is the app's viewport: `@media (max-width: 900px)`
  means a 900px frame. Check 1166px and 760px.
- **Inset optically, not by the box.** The space above the first line of a card (its title, a
  KPI label) equals the card's 16px side padding, measured to the top of the capitals. A line box
  adds about 6px of invisible space above 14px capitals, so a title with 16px padding sits about
  22px down and the card reads top-heavy. Trim it: `text-box: trim-both cap alphabetic` on card
  titles, `text-box: trim-start cap alphabetic` on the first line of a tile. Never give a card
  header a fixed `min-height` for a control it may not have: a 20px title centered in 32px drops
  another 6px. When a header does hold a control, the title centers on it and the control's top
  edge carries the inset; then every header in that row takes the control's height, so titles
  share one line across the row (the example's `.row:has(...)` rule).
- **Radius nests:** cards 14px, controls and popovers 10px (Basecoat's own controls are 8px: set
  10px with the 32px height), chips and tooltips 6px. Where one rounded surface sits within 8px of
  another, the inner radius is the outer one minus the gap (a 10px popover with 4px padding holds
  6px options; a 10px track with a 2px inset holds 8px segments); a copied radius pinches the
  corner. Pill only for status pills, delta pills and
  counters.

### Surfaces

The example defines these as tokens; take them from there, never write a one-off shadow.

- **Depth is a shadow stack, not a border:** a 1px ring that stands in for the border, a tight
  shadow under it, a soft one for ambient depth, in neutral ink, never colored. `--shadow-e1`
  rests (cards), `--shadow-e2` floats (menus, popovers, the picks list). A surface carries one
  depth cue: a stack replaces a border, never sits on top of one.
- **Dark collapses every stack to one white ring** (`0 0 0 1px` white at 9% for e1, 14% for e2):
  shadows can't be seen on a dark surface, and a light stack left on reads as a smudge.
- **Hairlines stay crisp:** dividers and table rules use `--hairline`, which is 0.5px on 2x
  screens and 1px elsewhere. Field borders stay real borders: they are part of the control.
- **An image** (a product photo, a flag, a logo) carries `outline: 1px solid` black at 10%,
  `outline-offset: -1px`, white at 10% in dark: never a tinted or accent edge, which reads as
  grime against the surface behind it.
- **No decorative gradients.** An area fill under a line fades with an eased gradient (the
  example's `easedFill()`, alpha along a smoothstep curve): a two-stop linear fade bands into
  visible stripes. To fade content out, use `mask-image`, never a gradient overlay, and never over
  a scrolling area, where it hides what the reader scrolls toward.

## 4. Hierarchy and type

- **Three sizes, each with its line height, set together every time: 20/28, 14/20 and 12/16px.**
  20 for the page title and figures (with `letter-spacing: -0.01em`, which Inter wants at that
  size), 14 for headings, body, labels and table cells, 12 for captions, axis labels, legends,
  deltas and the footer. Never set `font-size` alone: `body`'s 20px line height is fixed, so a
  12px element inherits loose leading and a 20px one cramped leading. Emphasis
  comes from weight or color, never a fourth size: a fourth size invites a fifth, and the page
  loses its scale.
- **Weights 400, 500, 600 only.** `b, strong { font-weight: 600 }`: the browser's default bold is
  700, a fourth weight on a page built on three. Set `font-synthesis: none`, since the variable
  Inter has no italic and the browser would fake one by slanting.
- **A finding, an instruction or an empty-state message is never 12px.** 12px is for labels of
  other things (axes, legends, deltas) and for supporting text: the header caption, an error's
  detail, the footer. A finding set at 12px is a finding nobody reads.
- **`tabular-nums` on HTML numbers only:** figures, number cells, deltas (ECharts draws its labels
  on a canvas, where it can't apply). Not on `body`: Inter's tabular digits also widen hyphens, so
  "2026-Q2" in a sentence spreads apart.
- **Smooth text once, on `body`** (`-webkit-font-smoothing: antialiased`): without it macOS draws
  Inter heavier, so 400 reads as 500 and 600 as bold.
- **Sentence case** for every title, heading, label and button. No uppercase eyebrows.
- **Three text colors:** ink (`--foreground`) for content, weak ink (`--weak`) for secondary
  text, muted (`--muted-foreground`) for captions and placeholders. No fourth gray, and no color
  literal outside `:root` and `.dark`: a hex in a rule is a color that misses the dark theme.
- **Prose lines stop at 72ch** (`max-width: 72ch`); headings wrap with `text-wrap: balance`,
  paragraphs with `text-wrap: pretty`.

## 5. Color

- **In the UI, the accent marks one thing at a time:** focus, a selected option, and a primary
  button when the page has an action (most dashboards have none). Accent used for decoration
  stops meaning "look here". Charts follow their own palette: a single series takes its first
  color (violet in the default look), and a selection dims the other marks rather than
  recoloring them.
- **Never red and green for good and bad in marks.** About 1 in 12 men can't tell them apart, and
  a rising cost in green reads as good news. A change around a baseline uses the diverging ramp.
  Green and red belong only to the KPI delta pill, beside its arrow and sign.
- **Status colors never color a series.** A red line for "Scenario B" reads as an alarm.
- **Ramps are the example's tokens:** `--chart-seq-1..9` for magnitude (one hue, even lightness
  steps), `--chart-div-neg-4..pos-4` for change around a baseline (orange below, gray at 0,
  violet above). Never interpolate between two hexes: the middle drifts gray or purple. A value
  printed on a fill takes ink when the fill is light and white when it is dark (the comments on
  the tokens say which steps), so it keeps 4.5:1.
- **Lines, points and outlines meet 3:1 on their surface.** A categorical slot under 3:1 has a
  `-strong` twin for them (`--chart-cat-3-strong`); a filled mark may be lighter only when its
  value shows as a label or in a table.
- **Color is never the only carrier:** every colored mark also has a label, a tooltip or a value
  in a table, and every status has a word or an icon.

## 6. Charts

### Chooser

Walk it top to bottom; take the first that fits.

```
What does the block answer?
├── One number, now, vs a comparison ───────────────▶ KPI tile, no chart
├── How it changed over time
│   ├── series more than 5× apart, or in different ─▶ small multiples: one panel per series,
│   │   units (signups ~600 vs conversions ~100)        stacked, sharing one x-axis
│   ├── this period vs the same period last year ───▶ two lines on one axis: current in the
│   │                                                   accent, last year in the context gray
│   ├── 1 to 3 series ──────────────────────────────▶ line chart, direct labels at line ends
│   └── 4 or more series ───────────────────────────▶ highlight the one that matters, the rest
│                                                       in context gray; or small multiples
├── How categories compare
│   ├── categories have an order (months, buckets) ─▶ vertical columns in that order
│   ├── up to 12 categories ────────────────────────▶ horizontal bars, sorted descending,
│   │                                                   value label at each bar's end
│   └── more than 12 ───────────────────────────────▶ top 10 bars + "Other", or a table
├── Two measures with different units, per category ▶ two aligned bar charts or a table;
│                                                       never a dual y-axis
├── Category × time, or category × category ────────▶ heatmap on the sequential ramp, values
│                                                       in the cells
├── Parts of one whole
│   ├── 2 or 3 parts ───────────────────────────────▶ one 100% stacked bar, or a donut
│   └── more ───────────────────────────────────────▶ sorted horizontal bars
├── Spread of values ───────────────────────────────▶ histogram
└── Look up exact values ───────────────────────────▶ table
```

Why the hard lines: a dual y-axis lets the author choose two scales and so invents a
correlation; bars read by length, so a bar axis that doesn't start at 0 lies; a pie past three
slices asks the reader to compare angles, which nobody does accurately.

Why small multiples past 5×: one shared axis scales to the largest series, so a series a fifth
its size moves through a sliver of the plot and its rises and dips read as a flat line. Compare
the series' peaks; above 5× apart, or in different units, give each its own panel. Stack the
panels in one card, each 120 to 160px tall with its own y-axis, and draw the x-axis labels under
the last panel only, so the eye reads the same weeks straight down. In ECharts, one instance
with a `grid`, `xAxis` and `yAxis` per panel, and `axisPointer: { link: [{ xAxisIndex: 'all' }] }`
so hovering one panel marks the same week in every panel.

### Every chart

- **Bars start at 0. Lines may zoom** their y-axis to the data, with the range visible on the
  axis. A rate's axis runs 0 to 100% unless the story is a small move, then say so in the caption.
- **No chart title inside the canvas:** the card heading is the title. No legend for one series.
  For 2 to 3 lines, label each line at its end instead of a legend; a legend makes the reader
  match colors back and forth.
- **Axis and grid recede:** axis labels 12px muted, hairline split lines on the value axis only,
  no axis line or ticks on the value axis, at most 6 value ticks with round steps.
- **Tooltips** show formatted values (copy.md) with the series name, never raw strings or ISO
  dates, and set `confine: true`: a card clips whatever overflows it, a tooltip included. Theme
  every ECharts surface the reader sees, not only the series: ECharts draws its tooltip as a white
  box, a white panel on the dark page. The example's `tooltipStyle()` takes its colors from
  tokens, and `transitionDuration: 0` stops it gliding behind the pointer.
- **One tooltip layout for every chart:** the category or period as its title, a row per series
  (square swatch, name, value right-aligned in weight 500 and `tabular-nums`), and a hint line
  when a click does something. Build every tooltip with the example's `tooltipHtml()`: ECharts'
  default sets values in weight 900 beside round markers, so a page mixing it with a custom
  formatter shows two tooltip styles.
- **A tooltip adds exact values; it never carries a mark's identity alone.** Pictures and
  keyboards never see it: every series is named by an end label or a data label.
- **A chart click that filters the page also has a control for the same field** (clicking a
  region bar, and the Region select), so a keyboard reader can do what the mouse does. Make the
  whole row clickable, its axis label included: a bar near zero is a 1px target.
- **End labels never collide:** where lines meet, `labelLayout: { moveOverlap: 'shiftY' }` moves
  one label off the other.
- **The period still filling is named.** The current month (or week, or quarter) at the end of a
  trend has fewer days than the rest, so its lower value reads as a drop. Label it "Oct 2026 to
  date" and compare it with the same days last year, as the example's `isPartial()` does; or end
  the trend at the last full period.
- **Design for the worst row, not the first:** the longest category name, a null, a zero, a
  single category, a negative change. Before handing over, find each in the data and look at the
  chart with it; a layout tested only on tidy rows breaks on the first real one.
- **A mark that filters on click looks clickable:** `cursor: 'pointer'`, a hover emphasis on the
  mark, and a last tooltip line "Click to filter the page", as the example does. A chart whose
  marks do nothing keeps the default cursor and no emphasis: a hover that promises a click and
  does nothing reads as broken.
- **Every chart has an accessible name:** ECharts `aria: { enabled: true }`, which describes the
  chart to screen readers; a chart drawn by hand in SVG or canvas gets `role="img"` and an
  `aria-label` that says what it shows ("Revenue by month, this year vs last year").
- **Draw charts again once fonts load** (`document.fonts.ready.then(render)`): a canvas keeps the
  fallback font it first drew its labels in.
- **Long category labels truncate**, they never shrink the plot: `axisLabel: { width: 160,
  overflow: 'truncate', ellipsis: '…' }`, with the full name in the tooltip.
- **ECharts needs hex colors** (it cannot parse `oklch`): read them from CSS variables as the
  example's `chartTheme()` does, so light and dark both work. `animation: false` on every chart:
  the host pictures the page, and an animating chart is pictured mid-flight.
- **Width follows the card.** Resize every chart on `resize`.

### Tables

Numbers right-aligned in `tabular-nums`, text left-aligned; header 12px weight 500 weak ink;
rows 36px tall, separated by `border-bottom: var(--hairline) solid var(--border)`, no zebra
stripes (right-aligned tabular numbers do the work stripes were compensating for). A text cell
holds one line (`white-space: nowrap; overflow: hidden; text-overflow: ellipsis`) with the full
value in `title`; numbers never truncate. A sortable column shows its direction. Over 50 rows,
page it (`pageSize`) rather than drawing them all.

## 7. Controls

- **One height: 32px** for every field and button.
- **One control surface:** every field, select, outline button and pressed segment is white
  (`--card`), raised by the same soft shadow ring as a card (`--shadow-e1`: a faint ring and two
  soft layers; in dark, the ring alone). Never a hard border: a dark 1px outline makes a control
  read as a box drawn on the page instead of a surface lifted off it. The ring is a shadow, so it
  doesn't add to the 32px. Basecoat draws each kind its own way (a transparent select on the
  paper, a fill on dark outline buttons); left alone, a header row of four controls reads as four
  libraries.
- **One focus style:** a 2px `--ring` outline offset 3px, set once on `:focus-visible`, as the
  example does. Never Basecoat's half-violet halo (2.1:1 on white) beside it, never `outline: none`.
- **Focus never strands.** When the focused element goes away (a removed chip, a closed list),
  move focus to its neighbor or to the control that opened it, as the example's chip field does;
  focus left on a removed node drops a keyboard reader back to the top of the page.
- **Hit areas are at least 24px** even when the mark is smaller: a chip's 18px remove button gets
  a `::before` inset of −4px. Controls are 32px, so this only matters for marks inside them.
- **Labels open their field:** `<label for>` on an input, `aria-labelledby` on a select's button,
  so a click on the label works and a screen reader names the field.
- **A search field** sets `autocomplete="off" spellcheck="false"`: suggestions and red squiggles
  cover the results the reader is looking for.
- **A button that starts a request** ("Try again", "Show more") ignores clicks while its request
  runs (`aria-busy="true"`): a second click fetches the same page twice.
- **Control chrome isn't text to copy:** `user-select: none` on segmented options, chips, control
  labels and card-header buttons, so a double-click on a period paints no selection. Never on
  figures, tables or the footer, which readers copy.
- **State never changes weight or size.** A pressed segment or a selected row keeps weight 500 and
  shows its state by color and surface: a bolder label widens its button and shifts every control
  after it.
- **Disabled is a token, not opacity:** text in the muted color on the sunken surface. Basecoat's
  `opacity: .5` reads differently on paper and on white. Prefer hiding a control that can't act,
  as "Clear selection" does.
- **Typing never runs a query per keystroke.** A combobox search (`loadOptions(text)`) or a text
  filter waits 300ms after the last key, Enter runs it at once, and a reply older than the latest
  request is dropped: a slow early reply would overwrite a later one.
- **Controls side by side share one height and one baseline.** An input, select, combobox, date
  field or segmented control next to a button (or next to each other) is exactly as tall as it,
  and their labels sit on one line. Set the height on every kind together, as the example's
  styles do: Basecoat's own default is 36px, so styling only the button or only the field puts a
  32px control beside a 36px one. The row then reads as misaligned even when nobody can say why.
  An icon-only button is a 32px square. When the user has allowed a browser, measure the row
  there: heights are where "looks right in the code" and "is right on screen" part.
- **Pick the control by the number of options:** 2 to 5 mutually exclusive options (a period, a
  grain, a comparison) are a segmented control: a sunken track whose pressed option is a white
  chip, with `aria-pressed`. More than 5 values are a Basecoat select (`div.select`: a button
  and a listbox popover), with `aria-multiselectable="true"` when the reader may pick several.
  A list too long to scan (more than 15 values) needs search: a single-select combobox. For
  several picks from such a list, narrow it first with a parent filter (country, then city) so
  the select stays short. Never a native `<select multiple>`, never a radio list.
- **A multi-select stays one 32px line.** With nothing picked it reads "All regions". With picks,
  it shows as many as fit as chips, each with a remove button, then a "+N" button that lists
  every pick (with their remove buttons); clicking anywhere else on the field opens the menu, and
  the list and the menu never show at once, as in the example. A single chip too wide for the
  field ends in an ellipsis. Never chips that wrap: each takes a line in a narrow field, and
  three picks grow a 32px field past 100px, pushing its whole row out of line. Never make the
  reader open the menu to remove a pick.
- **Periods are presets** (a segmented "3M · 12M · YTD"); add two date inputs only when the user
  asked for arbitrary ranges. With them, a start after the end shows "Start is after end" under
  the pair, and the queries keep the last valid range.
- **"Clear selection" exists only while there is a selection**, in the header of the chart that
  made it, as a ghost button. A permanent clear button does nothing most of the time.
- **Every control has a visible label** or sits in a group whose heading names it. A placeholder is
  not a label: it disappears when the reader types.

## 8. States

Each block designs four states, and each holds the block's loaded size so nothing jumps.

- **Loading:** a skeleton shaped like the content, in the same box: the chart area, a 28px bar for
  a figure. Give every block a `min-height` equal to its loaded height. A spinner collapses the
  page and then everything below it jumps when data lands.
- **A skeleton must show:** paint it in the sunken tone (`--sunken`), not Basecoat's `--muted`,
  which is near-white on a white card, so loading reads as a blank card.
- **Updating** (a control changed, the old result is still shown): keep the old result and dim it
  to 0.6 opacity; never blank it. A figure from the old scope must never show under the new
  scope's label: dim it, or skeleton it, until the new result lands.
- **Empty:** say what scope is empty and how to widen it, in the block's own space: "No orders in
  APAC in this period. Choose a longer period or another region." Never a bare 0 or a blank card.
- **UI that appears later takes reserved space:** a "Clear selection" button, a hint, a busy cue
  sits where it pushes nothing, or its row already holds its height (the example's header rule).
- **Changes a control causes are announced:** the header caption and the KPI row carry
  `aria-live="polite"`, so a screen reader hears the new scope and figures without hunting.
- **An app that refreshes on a timer pauses while the tab is hidden** (`document.hidden`); a
  query every minute nobody sees is load on anfra for nothing.
- **An inner scroller** (a wide heatmap or table inside its card) sets `overscroll-behavior:
  contain`, so reaching its end doesn't scroll the page under the reader.
- **Error:** in the block, never a page-wide banner: what failed in words ("Couldn't load revenue
  by region"), `error.message` under it in caption size, in ink. Never a whole red sentence. A
  `PermissionError` is not a failure: "You don't have access to this data". A `TransportError`
  gets "Couldn't reach the server" and a "Try again" button that calls `app.refresh()`. One
  failure across a row of tiles is one message over the row, not a copy per tile.

## 9. Motion

Only the reader's input moves anything. The host pictures the page at any moment, so anything
moving on its own is pictured mid-flight, and a dashboard read daily wears out a flourish by the
second visit.

- **Nothing animates on load:** no fade-in, slide or stagger of blocks, no figure that counts up,
  no chart entry (`animation: false`), nothing that waits for a scroll to appear.
- **Press shows by color, never by size.** A button or field never scales or shifts on click
  (DESIGN.md's 0.97 press belongs to the web app, not a Data App): a control that shrinks under
  the pointer reads as a toy in a dense tool. **Hover:** color only, 160ms, under
  `@media (hover: hover) and (pointer: fine)`. **Popovers:** fade and scale from 0.97 toward their
  trigger in 150ms, never slide. All on `ease-out`, all under 300ms.
- **Name the properties** (`opacity`, the color properties), never `transition: all`,
  which also animates sizes and colors you didn't mean to move.
- **Updating dims after 150ms and restores at once.** A cached result lands in under 150ms;
  dimming for it makes the page flicker on every click.
- **A theme switch doesn't animate:** flip the `dark` class with transitions off for two frames
  (the example's `theme-switching` class), or every control tweens its colors on its own clock.
- **Reduced motion drops movement and keeps fades:** no popover scale, slide or skeleton pulse;
  short opacity and color fades stay, because they carry state without travel.

## 10. Unslop before you hand over

Agents add by default, and every addition dilutes the lead. Before handing over, run the unslop
pass in [unslop.md](unslop.md): subtract what breaks nothing, sweep the tells (color, type,
layout, decoration, icons, copy, motion), and check the page still says something only this
data could. It runs in every look, and what it finds gets fixed.

## 11. Floors: every look

However wild the design, these hold, because breaking them makes the page wrong or unusable
rather than different. design-review.md walks them first, as F1 to F7.

- **Honest charts:** bars start at 0, no dual y-axis, no pie past 3 parts.
- **Numbers carry their unit and their comparison**, in one precision per measure, as copy.md
  says.
- **Every block has its loading, updating, empty and error states**, in its own loaded size, and
  an old figure never shows undimmed under a new scope's label.
- **Text is readable:** 4.5:1 contrast for text and 3:1 for chart marks, in light and dark;
  nothing the reader must read is under 12px. Control edges are the soft ring above, by the
  owner's choice: a white control raised off the page tone.
- **Controls work for everyone:** real buttons and inputs, labels, one visible focus ring, names
  on icon buttons, and controls side by side sharing one height (so a multi-select stays one
  line).
- **Nothing hides behind hover:** every colored mark is named by a label, and every chart click
  has a control that does the same.
- **Picture-safe:** no chart animation, nothing waiting for a scroll or a hover to appear.
