---
name: build-data-app
description: Build, change or review the design of an Anfra Data App (one self-contained HTML file in the repo's `apps/` that queries datasets, and can trace fields' lineage, through the `Anfra` SDK global and renders them with its own HTML, CSS and JS), then check it in `anfra serve`. Use for a dashboard, report, page or interactive analysis over the repo's datasets, and to review, critique or polish an existing app's layout, charts and copy.
---

# Building a Data App

A **Data App** is one self-contained HTML file under the repo's `apps/`. `anfra serve` lists it by
its `<title>` and runs it in a sandboxed frame where the SDK is already on the global `Anfra`. The
SDK supplies data and state; every pixel is yours.

This skill has two jobs:

- **Build or change an app:** steps 1 to 6.
- **Review an app's design:** [Review an app's design](#review-an-apps-design), when the user asks
  to review, critique, audit or polish an app that exists.

References, read when a step needs them:

- [references/page-design.md](references/page-design.md): how to plan and lay out the page: the
  lead, the page anatomy, layout recipes, type, color, the chart chooser, controls, motion and
  states. **Read it at step 2 of every build, in every look.**
- [references/copy.md](references/copy.md): titles, headings, number and date formats, empty and
  error text, the footer. Read it with page-design.md.
- [references/design-review.md](references/design-review.md): the review procedure and its
  checklist. Read it for a review, and at step 5 of every build.
- [references/api.md](references/api.md): every SDK call, the result shape, the plugins, errors.
  **Read it before writing any code; do not invent APIs.**
- [references/plugins/](references/plugins/): one file per built-in plugin beyond `core`
  ([export](references/plugins/export.md), [render](references/plugins/render.md),
  [lineage](references/plugins/lineage.md)). Read one only when the agreed design uses that plugin,
  and check `Anfra.plugins.has(name)` before using it.
- [references/downloads.md](references/downloads.md): the download button for exports and pictures.
  Read it only when the agreed design lists a download.
- [references/example.html](references/example.html): a complete app in the default look, laid
  out as page-design.md says: header with page controls, KPI row with comparisons, a trend beside its
  breakdown, a footer of definitions. Start a new file from its skeleton, or from the closest app
  already in `apps/`.
- [references/DESIGN.md](references/DESIGN.md): the anfra web app's design guide, the source of
  the default look's tokens (colors, type, radii, chart palette). Read it when you build in the
  default look, for its tokens only: its sidebar, sign-in sheet, Tailwind classes and logo rules
  belong to the web app, not to a Data App.

## 1. Learn the datasets

```sh
anfra show                 # every dataset: models, fields (dimensions, measures), metrics
anfra show <dataset fqn>   # one dataset
```

The fqn is what a query's `dataset:` takes. A field is `model.field` (`orders.created_at`), a
dataset metric its bare name. A measure is already aggregated: select it as it is, never wrap it
in `sum()`. Read labels and descriptions: they carry the modeler's caveats on grain, fan-out and
which date to use. For a metric's definition, read its `*.dataset.aml`.

If a number the app needs has no field or metric, add it to the AML with the [](../aml/) skill
rather than computing it in JavaScript.

## 2. Agree on the design

Restate the request as one result the user can check ("revenue for the last full week against
the week before, with a country picker"). Ask only what changes the numbers: what a business word
means, which date counts, what to exclude, each with your recommended answer.

Also ask which look the user wants, unless they already said: the **default anfra style**
(recommended), **something creative** (you choose the palette and type), or **their own design**
(a brand, colors or a reference to follow). See [Look and feel](#look-and-feel).

Then read [references/page-design.md](references/page-design.md) and
[references/copy.md](references/copy.md), and write the **page plan**: the lines of
page-design.md, section 1. The plan comes before the queries because it decides them: a
comparison in the lead needs its own measure, a breakdown its own query.

Then write the declaration list: every `createQuery` (dataset, dimensions, measures),
`createFilter`, `createDateDrill`, `mapControl` and `mapCrossFilter`, with real field names, how
each query renders (chart or table; default ECharts 6 from jsDelivr, as in the example), and which
blocks, if any, the reader can download from, one line each with its formats (`trend: CSV`). List
a download only when the user asked for one, or propose one where it plainly helps (a detail
table's data) and let them decide; none is the default. Leave pictures (PNG, PDF) to the host,
which offers them for every block and the whole page, unless the user asks for them in the app.
Show the plan and the list to the user together and get a yes before writing the file. When the
data cannot answer the request, say so and offer the nearest thing it can.

### 2.1 Lineage

Lineage explains and governs a metric rather than showing its value: it answers what the metric is
built from, the dimensions, measures and other metrics it depends on, and the models and source
tables behind them. Add it when the user asks for it, or when the request is about tracing, understanding
or governing a metric: "how is revenue calculated", "which tables feed this KPI", a field catalog,
a definitions panel beside a chart. An app that only shows numbers needs none.

When it is in, add to the declaration list which fields the app traces, when (on load, or when the
reader picks a field or clicks a chart), and how the diagram is drawn (see
[references/plugins/lineage.md](references/plugins/lineage.md)).

## Look and feel

An app must look finished without the user asking: native controls, default buttons and unstyled
text are not acceptable. [references/page-design.md](references/page-design.md) and
[references/copy.md](references/copy.md) are the baseline for every look; how far the page may
depart from them depends on the look the user chose. Their floors (page-design.md, section 11) hold
in every look.

- **Their own design:** take its colors, type, brand and layout from what they gave. page-design.md
  fills whatever their design doesn't say.
- **Creative:** go as far as the user wants: layout, type, palette, density, chart style. Add a
  direction to the page plan before any CSS: the user's words for the look, a named tradition to
  borrow from (a newspaper's data desk, Swiss editorial, a trading terminal), 5 to 8 rules you
  can check in the stylesheet ("one typeface, two weights", "hairlines only, no fills"), and the
  palette as hex values drawn from the data's domain, at most two typeface families (a display
  face for the title and figures, a text face for the rest). Drop violet and Inter unless the
  user names them: they are the default look's, and a creative page that keeps them reads as the default
  with the dials turned. Make one distinctive move and repeat it on every block; a page of
  unrelated effects has no point of view. Keep the floors (page-design.md, section 11), and light
  and dark (`prefers-color-scheme`).
- **Default anfra style:** start from [references/example.html](references/example.html), which
  applies the tokens in [references/DESIGN.md](references/DESIGN.md) to
  [Basecoat UI](https://basecoatui.com), a plain HTML/CSS component library. Use its tokens as
  CSS variables, its three text sizes (20, 14, 12px), 32px controls and the chart palette (violet
  first). Load Inter from the CDN with a `system-ui` fallback.

In every look:

- **A single HTML file uses Basecoat** (pinned CDN CSS and JS, as in the example). Use shadcn
  (`shadcn-vue` for Vue) only when the app is written in React or Vue.
- **Controls follow page-design.md, section 7:** a segmented control for 2 to 5 options, a
  Basecoat select for more, a multi-select that stays one 32px line with removable chips and
  "+N", as in the example. Never the combobox's multi-select chips, which wrap and grow the
  field, and never a native `<select multiple>`. Basecoat has no date picker: offer period
  presets, and add two styled `<input type="date">` only when the user wants arbitrary ranges.
- **Never put controls in a Basecoat `.card`.** It is a flex column that clips its overflow, so a
  dropdown is cut off at the card's bottom edge. Page controls sit in the header row, as in the
  example. With the user's go-ahead for a browser (step 5), open every dropdown before handing
  over; without it, list the dropdowns under what you could not check.
- Charts follow page-design.md's chart rules. If a `dataviz` skill is available, use it for what
  page-design.md does not cover. Give ECharts hex colors: it cannot parse `oklch`.

## 3. Write and check the queries

A query's `aql` is AQL: write it with the [](../aql/) and [](../write-aql/) skills. A Data App adds
four rules, because the SDK does not run your AQL as written: it sends it with the reader's state
(filter values, the cross-filter selection, sorts, date drills, the page), which anfra applies to
the query's `explore { }` before it runs.

- **Write an `explore { }`**, optionally after query-local `dimension` and `metric` declarations.
  Any AQL expression works inside it. A pipe-style query (`orders | select(...)`) runs, but fails
  as soon as a reader touches a control, sort or date drill mapped to it.
- **Alias every dimension and measure** (`revenue: sum(orders.amount)`). The alias is the row key
  in `query.result.rows`, and what `sorts` and `setSort` name.
- **No limit.** Paging is the SDK's (`pageSize`, `fetchMore`).
- **Nothing a reader changes goes in the AQL text.** Static conditions go in `filters` or
  `having`; a reader's choice is a control (`createFilter` + `mapControl`), never a string built
  into the query.

Run each query before it goes in the app, so a mistake surfaces here rather than in a frame:

```sh
anfra query --dataset <fqn> '<aql>'
```

Check its headline number against a direct SQL query on the source, or a simple invariant (parts
summing to the total). Trace each lineage target the same way, and check its `refers` path is the
one you expect:

```sh
anfra lineage --targets '[{"parent":"<dataset fqn>","child":"<metric>"}]'
```

## 4. Write the file

One self-contained `.html` file in `apps/` (subfolders group apps in the list), with a `<title>`
the list shows.
`apps/sales/overview.html` opens at `/apps/sales/overview`. Follow the example's skeleton: declare
everything, `subscribe` a render function, `execute()`, then wire controls and clicks to set state
and `execute()` again. Keep its two layers apart: the look (styles, markup, chart theme) and the
SDK wiring. Lay the page out in the agreed plan's order with page-design.md's anatomy and recipes, and
write every title, heading, number and state with copy.md.

Delete what the plan doesn't use from the example together with its script: a control's markup
without its JS (or JS without its markup) throws on `null` before `execute()`, and the page shows
skeletons forever. Replace the example's header comment with one line on what the page is for and
the plan's decisions (the lead, the defaults, what was left out), so the next change starts from
them.

The frame is a **sandbox** (`allow-scripts` only, opaque origin) under a **content policy**. Write
for it:

- **Data comes only from the SDK.** The frame has no network of its own: `fetch`, XHR, WebSocket
  and `sendBeacon` are blocked but to the CDNs below. Never call anfra's API or any other service
  from the app.
- **Libraries, styles and fonts only from cdn.jsdelivr.net, cdnjs.cloudflare.com and Google
  Fonts** (fonts.googleapis.com, fonts.gstatic.com), each by its `https://` URL with a pinned
  version. A script, style or font from anywhere else is blocked, esm.sh and unpkg.com included:
  load an npm package from jsDelivr (`/npm/<pkg>@<version>/<file>`), and an ES module from its
  `+esm` build (`https://cdn.jsdelivr.net/npm/<pkg>@<version>/+esm`). Images may come
  from any `https://` server. When the user needs another origin (their own CDN), add it to the
  repo's `.anfra/config.yml` under `apps.allow_origins` (`[https://cdn.acme.com]`, an origin with
  nothing after it), and tell them anfra Cloud keeps its own list, which an Admin edits in the
  Organization's settings.
- **Every CDN `<script src>` and stylesheet `<link>` carries its hash:** `integrity="sha384-…"
  crossorigin="anonymous"`, as in the example, so a library changed at its CDN fails to load
  rather than runs beside the reader's data. jsDelivr shows a file's hash on its page; otherwise
  `curl -s <url> | openssl dgst -sha384 -binary | openssl base64 -A` gives what follows `sha384-`.
  Load fonts from `@fontsource` on jsDelivr, as the example does, rather than Google Fonts' CSS,
  which differs per browser and so cannot be hashed.
- `localStorage`, `sessionStorage` and cookies throw: keep state in variables. This includes
  Basecoat's theme switcher, which stores the mode: follow `prefers-color-scheme` instead, and set
  the `dark` class in `<head>`, as the example does. A script at the end of `body` runs after a
  light first paint, and `anfra serve` reloads the app on every save, so a dark-mode reader would
  see a white flash each time.
- Forms, popups, navigation, `<iframe>` and `<base>` are blocked: use buttons and `change`
  listeners, never `<form>`.
- **The app is its one HTML file: nothing it loads may be a file of the repo.** No relative `src`
  or `href` (`./chart.js`, `logo.png`, `../shared/app.css`). Put your own scripts and styles
  inline (`<script>`, `<style>`), images inline as SVG or a `data:` URI, and libraries and fonts
  from a CDN as above. Never split an app into several files or make one app load another's, and
  never add anything but `.html` apps under `apps/`.
- Every query is an `explore { }`: controls, cross-filters, sorting and date drills only apply to
  one.
- Mark the page's structure in the HTML, as [references/api.md](references/api.md#structure)
  shows: `data-anfra-container` and `data-anfra-block` on the parts of the page, with a
  `data-anfra-label`, and `data-anfra-query` / `data-anfra-control` on the elements each query
  and control draws into. Every query and control sits in a block: a standalone control gets a
  block of its own, a chart's own control goes in the chart's block. The Inspect panel shows the
  tree, finds each part on the page, and gives the user a handle for it to hand back to you.
  Mark every part; it is one attribute each.

Render a selection without rebuilding the chart. A query that drives a cross-filter is not re-run
by its own selection, so its result is unchanged when the reader clicks: restyle the marks it
already draws (dim the unselected ones) rather than recreating the series, which redraws every
mark and drops the hover state on each click. With ECharts, merge with a plain
`setOption(option)` for a selection change and keep `replaceMerge` for a new result.

Add a download only where the agreed design lists one, never on your own initiative, and read
[references/downloads.md](references/downloads.md) first: a block gets at most one download
control, in its header, with every agreed format an item of it, never a button per format; a
picture of the whole app is one control in the page's header. Don't build a CSV from
`query.result.rows`: a paged result holds only the pages loaded. Any app may be pictured from the
host's toolbar, so keep every app render-safe: chart entry animations off (ECharts
`animation: false`), images and web fonts from servers that send CORS headers (jsDelivr, Google
Fonts), and `preserveDrawingBuffer: true` on a WebGL chart.

Render each query's states as page-design.md (section 8) says, each in the block's loaded size:
`executing` with no result yet (a skeleton), `executing` with the previous result (dimmed),
`success`, success with no rows (the empty scope, in words), and `error` (what failed, then
`error.message`). Log `result.columns`, `result.rows[0]` and `result.debug?.executedAql` on each
query's first success: the real row keys, value types and the AQL that ran. Close the page with
the footer copy.md describes: each measure's definition, the date basis and the as-of date.

## 5. Run and check it

First run `anfra validate` and fix every `data_app` warning it reports: a file referenced beside
the app (every `src`, `href` and CSS `url()` must be `https://`, `data:` or a `#` fragment), or a CDN
script or stylesheet without `integrity`.

```sh
anfra status               # is a server already running for this repo, and where?
anfra serve                # if not: http://127.0.0.1:7878/apps/ (or the free port it prints)
```

Leave it running: it reloads the open app whenever its file, or the AML, changes. Lineage is the
exception: run `anfra ingest` after an AML change, or it shows the old definitions. Its header shows
the repo's problems (AML that does not compile, a data source not configured). Its Inspect panel
has two tabs: **Structure**, the tree of marked parts, with a problem count for markup it could
not read (an unknown query name, a block inside a block), and **Data**, each query's state,
executed AQL and error, in how many places it is drawn, and a Locate button. A query that says
"Not drawn yet" has no element marked with it.

If you can drive a browser, ask the user once before you start it (it opens processes and windows
on their machine); with their yes, open the app, exercise every control and click, and read the
console of the app's frame, not just the page. Otherwise ask the user to, and to report what they see
against what they expected, with that console. Read errors against
[references/api.md](references/api.md#errors): a `ValidationError` names a declaring line (or an
`Anfra.lineage` call with an empty or malformed target list; an unknown field comes back
as that target's `error` or an empty diagram); a
`QueryError` carries anfra's message about the AQL, or names the bad entry (`filters[0].field`).
Compare a misbehaving query's `executedAql` with what you meant, and fix the smallest thing that
explains it. If you could not check the interface, say so rather than claiming it works.

Then check the design: run [references/design-review.md](references/design-review.md) as a
self-check. Walk its checklist against the code, then, if the user allows a browser, look at the
page in light and dark at a 1166px and a 760px frame, and fix every Blocking and Should finding
before you hand over. First drafts fail the same few checks (no lead, one equal card per measure, raw
numbers, a legend on a single series): catching them here costs one pass, while each one the user
catches costs a round of feedback.

## 6. Hand over

Tell the user, in their terms: what the page leads with and shows, how to use each control, its
URL, the assumptions you made, and what you checked, design findings you fixed included. When a
later request names a part by its handle,
`[Revenue over time](data-anfra-block="trend")` or a bare `data-anfra-block="trend"`, search the
file for that attribute: it is the element the user means. A `127.0.0.1` URL opens only on this machine; `anfra
serve` has no authentication, so ask before serving it beyond loopback (`--addr`). Make each
requested change the same way: restate it if it changes what a number means, change the smallest
thing, and check that the agreed result still holds.

## Review an app's design

When the user asks to review, critique, audit or polish an app (theirs, a demo, or one you built
earlier), follow [references/design-review.md](references/design-review.md) with
[references/page-design.md](references/page-design.md) and [references/copy.md](references/copy.md) as
the rules.

1. Find the file in `apps/` (the user may name it by its `<title>`, its URL or a handle), read it
   whole, and note which look it is in. In the default look every check is a finding. In their
   own or a creative look only the floors and the story are; other departures from the baseline
   are choices, which you may offer as suggestions.
2. Walk the checklist against the code first. Then ask before opening a browser or taking
   screenshots (design-review.md, section 1); without a yes, finish from the code and say which
   checks only a look could settle.
3. Report the findings table, most severe first, each pointing at a part by its handle. Change
   nothing yet: the user picks what to fix.
4. Fix what they picked, the smallest change each, without changing what any number means. A
   finding that needs a different query (a comparison the page lacks, a lead it can't compute)
   goes back through build steps 2 and 3 for that query (agree on it, then write and check it).
5. Walk the checks the fixes touched again, from the code and, if the user allowed it, in the
   browser, and hand over as step 6 says.

## Related skills
* [](../aml/) — add the fields and metrics an app needs.
* [](../write-aql/) — write and validate the queries.
* [](../setup-repo/) — set up the repo and its data source first.
