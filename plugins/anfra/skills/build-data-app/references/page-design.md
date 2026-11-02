# Designing a Data App page

Read this at step 2, before the declaration list. It holds the calls the example can't make for
you: what the page leads with, which form each block takes, how numbers read. The look itself
(tokens, type, controls, cards, tooltips, states) is already built in
[example.html](example.html): keep its parts when you adapt it instead of restyling them.

How far a page may depart from this file depends on the look:

- **Default anfra look:** follow it. Its values are defaults, not a spec: change one when the
  content needs it, and say why in a comment.
- **Their own design:** theirs wins wherever it speaks; this file fills the gaps.
- **Creative:** a starting point. Only the floors (section 8) hold.

The frame `anfra serve` gives the app is about 1166 × 834px on a laptop and scrolls. The reader
decides from the first view whether to read on. Left to its defaults, an agent lays a page out by
inventory, one equal card per measure, and the reader can't tell what matters: most of this file
gives the page a top.

## 1. The page plan

Write it with the declaration list (step 2), one line each. Where you must guess, write the guess
and mark it "(assumed)": the user corrects a written guess in one reply.

1. **Reader and decision:** who opens the page, when, and what they do after reading it ("GMs,
   at the Monday meeting: which hotel needs a push this week"). The moment sets the default period.
2. **The lead:** the one number the decision turns on, and what it is compared with ("RevPAR, year
   to date vs the same dates last year"). The other requested measures join it in the KPI row.
3. **Blocks in reading order:** for each, the question it answers, its form (section 3) and its
   query. For each control, the question it lets the reader ask; a control no question needs is
   left out.
4. **First view:** the header, the lead and the top of the main chart fit in 1166 × 834px. What
   this reader doesn't need is left out, not added as one more card.
5. **Defaults that show the finding:** open on the period and comparison where the story is (the
   last full month vs the same month last year), never all time with no comparison.

## 2. Layout

Top to bottom: header (the `h1`, one caption line with the scope and basis, page controls on the
right of the same row), the lead, the main chart, detail, then the footer. Leave out a part the
plan has nothing for.

- **The lead** is a KPI row of 2 to 4 tiles, the lead tile first; or, when one number decides and
  a query can say why, a hero: that figure beside its computed sentence. Past 4 measures, the
  rest go in a table: a fifth tile means the plan has no lead.
- **A KPI tile** is a label, the figure in ink, then a delta pill (arrow and signed change, tinted
  green or red by whether that direction is good for this measure, gray when neither) and its
  comparison: "[↑ +6.2%] vs last year". Pills only in KPI tiles; in tables and charts a change
  is signed text, because a pill on every number marks none of them.
- **One 12-column grid:** every row is a `.row` of spanned cards, as in the example, so edges
  between rows match exactly or differ by a whole column, never by a few pixels. Spans follow the
  content: a time series 6 columns or more, labeled bars 4 or more, a table 6 or more. The chart
  that explains the lead takes the wider span (8 + 4); 6 + 6 when its companion needs the room.
  Tiles in one row share one span. Cards in a row share one height.
- **Controls:** page-wide ones in the header row, at most three (more go in a bare row under it);
  a control that changes one chart in that chart's card header. Never in a card body.
- **Footer:** a definition list, one entry per measure (the term over one clause of at most 15
  words, from its AML description) under its KPI's column, then one line with the comparison,
  the date basis and the as-of date. Never one paragraph: the reader would read all of it to find
  one measure.

## 3. Charts

Walk the chooser top to bottom; take the first that fits.

```
What does the block answer?
├── One number vs a comparison ──────────────────────▶ KPI tile, no chart
├── How it changed over time
│   ├── series more than 5× apart, or in different ──▶ small multiples: a panel per series,
│   │   units                                            stacked, sharing one x-axis
│   ├── this period vs the same period last year ────▶ two lines, current in the accent,
│   │                                                    last year in the context gray
│   ├── 1 to 3 series ───────────────────────────────▶ lines, labeled at their ends
│   └── 4 or more ───────────────────────────────────▶ highlight the one that matters, the
│                                                        rest in context gray
├── How categories compare
│   ├── ordered categories (months, buckets) ────────▶ columns in that order
│   ├── up to 12 ────────────────────────────────────▶ horizontal bars, sorted, value at the end
│   └── more than 12 ────────────────────────────────▶ top 10 + "Other", or a table
├── Two measures in different units, per category ───▶ two aligned bar charts, or a table
├── Category × time, or category × category ─────────▶ heatmap in one hue, values in the cells
├── Parts of one whole: 2 or 3 parts ────────────────▶ one 100% bar or a donut; more: sorted bars
└── Exact values to look up ─────────────────────────▶ table
```

Why the hard lines: a dual y-axis lets the author pick two scales and so invents a correlation;
bars are read by length, so a bar axis that doesn't start at 0 lies; nobody compares pie angles
past three slices; and one axis shared by series 5× apart turns the small one into a flat line.

- The card heading is the chart's title: none inside the canvas. No legend for one series; 2 or 3
  lines get labels at their ends instead.
- Every series is named without hover (an end label, a data label or a table column): pictures
  and keyboards never see a tooltip. Tooltips go through the example's `tooltipStyle()` and
  `tooltipHtml()`, so every chart's tooltip looks alike and follows dark mode.
- In the default look, a chart with one series draws it in violet (`chartTheme().cat[0]`), and a
  chart with several takes the palette in order from violet. Violet is what makes the page read
  as anfra: never pick a later color to match a series on another chart (teal bars because the
  conversions line is teal), which leaves the page's main breakdown off-brand.
- A selection dims the other marks rather than recoloring them. Red and green never mean good and
  bad in a chart (about 1 in 12 men can't tell them apart), and status colors never color a series.
- A period still filling is labeled "to date" (the example's `isPartial()`), or the trend ends at
  the last full period: a partial month's lower total reads as a drop.
- A mark that filters the page on click looks clickable (pointer, hover emphasis, "Click to filter
  the page" in its tooltip), and a control does the same for keyboard readers.
- Design for the worst row: the longest name (truncated with an ellipsis), a null, a zero, a
  single category. Find each in the data and check the chart holds.

Tables: numbers right-aligned in `tabular-nums`, text left-aligned, one line per cell with the
full value in `title`, and `pageSize` past 50 rows.

## 4. Controls

Pick each control by what it sets:

```
Views that swap which blocks show ─────────▶ Basecoat .tabs, above the blocks they swap
2 to 5 options, one pick (period, grain) ──▶ the example's segmented control
More than 5 options, one pick ─────────────▶ Basecoat div.select; a combobox past ~15 values
Several picks ─────────────────────────────▶ the example's multi-select: one 32px line,
                                              removable chips, then "+N"
A date range ──────────────────────────────▶ presets; two date inputs only if the user asks
```

Never a native `<select>`: the browser draws it, so it reads as a second library beside Basecoat.
Controls side by side share the example's 32px height and white surface; Basecoat's own default
is 36px, so restyling one kind alone leaves a row that looks off without anyone knowing why. Every
control has a visible label. "Clear selection" shows only while there is a selection.

## 5. Words and numbers

- **Title:** what the page is about, in 1 to 4 of the reader's words, sentence case:
  "Subscription growth". Never "Dashboard", "Overview" or "Analytics". Keep `<title>`, `h1` and
  `createApp({ title })` the same.
- **Card heading:** what is plotted and against what: "New signups by week, vs the previous 13".
- **One name per concept** in tile, chart, table, tooltip and footer, taken from the field's label
  in sentence case. Never a field name, query name or alias.
- **A sentence on the page is computed** from a query result, and claims only what a query
  measured: no "strong", no "driven by". A finding typed as static text is wrong the day the data
  changes.
- Format with `Intl.NumberFormat` and `Intl.DateTimeFormat`, from the result's strings, one
  formatter per kind. The SDK doesn't carry a measure's `format`: read currency and precision from
  its AML when you write the app, and keep one map from alias to formatter with a comment naming
  the AML. Never guess a currency from a field name.

| Kind | Format |
| --- | --- |
| Money | compact past 10,000 in figures and axes ($1.2M), full in tables and tooltips |
| Count | grouped (12,480) |
| Rate | percent, 1 decimal (4.7%); its change in points (+1.2 pts) |
| Change | signed, with its comparison ("+2.3% vs last year"); minus is − (U+2212), never "−0.0" |
| Date | short month, never ISO (Oct 19, 2026; Oct 2026; Week of Oct 13) |
| `null` | "No data", or an en dash in a table; never 0, which means something else |

One precision per measure across the page. No "please", "Oops" or exclamation marks: the page is
a tool.

## 6. States

Each block has four states, each in its loaded size so nothing jumps (the example's
`renderStatus()` and `renderKpis()`):

- **Loading:** a skeleton shaped like the content.
- **Updating:** the old result stays, dimmed. An old figure never shows undimmed under the new
  scope's label.
- **Empty:** the scope that is empty and how to widen it: "No signups in Brazil in this period.
  Choose a longer period."
- **Error:** what failed, in words ("Couldn't load signups by week"), then `error.message`. No
  access and a lost connection read differently from a failure, as in the example.

Nothing moves on load: no fade-in, count-up or chart entry. The host pictures the page at any
moment, and a page read every day wears out a flourish by the second visit.

## 7. Unslop

Agents add by default, and each addition dilutes the lead. Before handing over, delete every
element whose absence breaks nothing, then remove these tells. Each is defensible alone; together
they read as a generated page:

- gradients, glass, glows, colored shadows, a different tint per card;
- uppercase eyebrows, Title Case headings, a fourth font size, weights above 600;
- an emoji or icon before every label, a pill or badge on every number;
- an "Insights" or "Key takeaways" card no query produced, "Welcome back", a caption that repeats
  its heading;
- puffery (powerful, seamless, robust) and judgments no query measured;
- cards that lift on hover but do nothing on click.

In their own design or a creative look, a tell stays only when the user asked for that element.

## 8. Floors, then the self-check

The floors hold in every look, because breaking one makes the page wrong or unusable rather than
different:

- Honest charts: bars from 0, no dual y-axis, no pie past 3 parts.
- Every number carries its unit, and every change its comparison.
- Every block has its loading, updating, empty and error states.
- Text meets 4.5:1 and chart marks 3:1 on their surface, in light and dark; nothing to read is
  under 12px.
- Real buttons and inputs, visible labels, a visible focus ring, and controls side by side at one
  height.
- Nothing only on hover, and nothing that animates on its own.

At step 5, read the file against these questions and fix every "no". First drafts fail the same
few, and each one the user catches costs a round of feedback:

1. Does the first view hold the header, the lead and the top of the main chart?
2. Is there one lead, first, with its comparison, and do the defaults show the finding?
3. Do spans follow the content, on one 12-column grid, with no control in a card body?
4. Is each block the chooser's form, with no legend on one series and no unsorted bars? In the
   default look, is every single-series chart violet?
5. Is each control the right kind for what it sets, with no native `<select>`?
6. Are titles, headings, names and numbers written as section 5 says?
7. Does every floor hold, and is every tell in section 7 gone?
