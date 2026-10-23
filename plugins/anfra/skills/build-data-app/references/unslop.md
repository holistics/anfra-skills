# The unslop pass

Run it on every build (step 5) and every review, in every look, and fix what it finds. A page
that reads as generated is never a style choice: in the user's own design or a creative look, a
tell stays only when the user named that exact element ("a gradient header, like our site").

Nobody spots a generated page from one element. They spot the pile-up: a gradient, glass cards,
an emoji per heading, an "Insights" card, a caption under every title. Each is defensible alone;
together they are a watermark. The pass removes the pile-up, then checks the page still says
something only this data could say.

## 1. Squint

Look at the page (or read its markup top to bottom) and ask: could this be any of ten thousand
dashboards? Name the elements that tie it to this dataset and this reader: the lead number, its
comparison, the breakdown that explains it. Everything else is up for deletion.

## 2. Subtract first

Walk every element (icon, badge, caption, divider, card, line of text) and ask: what breaks if
this is gone? If nothing, delete it. Walk again after the first round: deletions expose each
other. Never restyle an element you are about to delete.

When the answer is "the reader loses a fact" (a warning, a status, a note on the data), strip the
decoration and design what carries the fact: a status pill beside one sentence, a caption under
the figure it qualifies, a row in the table. Removing a tell is half the fix; a bare paragraph
left where a callout was reads as unfinished, not as polished.

## 3. Sweep, in this order

Fix each tell where you find it. Color first, because it is the loudest.

| Layer | Tell | Fix |
| --- | --- | --- |
| Color | Gradient backgrounds, gradient text, gradient borders | Flat page tone; solid ink text |
| Color | Neon or glowing accents, dark-by-default for "premium" | The look's palette; dark only through `prefers-color-scheme` |
| Color | Violet or indigo nobody chose (creative or own look) | A palette drawn from the data's domain or the user's brand |
| Color | A different tint per card, rainbow KPI tiles | One surface; color only on what carries meaning |
| Type | Uppercase eyebrows over every block | Sentence case, or delete the eyebrow |
| Type | Heavy display weight (700-900) on titles, a fourth size | The three sizes and three weights |
| Type | Title Case headings | Sentence case |
| Layout | Equal cards chosen before the content (three-up, bento) | Spans that follow the content (page-design.md) |
| Layout | A hero banner or welcome strip above the data | The header, then the lead |
| Decoration | Glass (`backdrop-filter`) with nothing behind it | A plain surface |
| Decoration | Blobs, orbs, grain, textures, decorative illustrations | Delete |
| Decoration | Colored or large drop shadows, glows, stacked depth cues | The shadow tokens, one cue per surface |
| Decoration | A pill or badge on every number, tinted chips around facts | Pills only for KPI deltas; plain signed text elsewhere |
| Decoration | Left-border callout stripes, tinted callout boxes | A status pill and one sentence in its block (step 2) |
| Icons | Emoji as icons, a sparkle on anything "smart" | One icon set, or none |
| Icons | An icon in front of every label or heading | Icons only where they replace a word |
| Copy | "Dashboard", "Overview", "Analytics", "Insights" as the title | What the page is about: "Subscription growth" |
| Copy | "Welcome back", "Here's what we found", "Unlock…", "Powered by…" | Delete |
| Copy | An "Insights" or "Key takeaways" card of sentences no query produced | A computed lead sentence, or nothing |
| Copy | A caption that restates its heading | Delete, or say what the heading lacks |
| Copy | Puffery and model words: powerful, seamless, robust, leverage, crucial, delve | The plain word, or the number |
| Copy | Judgments the data didn't measure: "strong growth", "driven by APAC" | The measured fact: "+14.5% vs the previous 13 weeks" |
| Copy | Em dashes and `·` chains joining three or more parts | Commas, periods, two-part labels |
| Copy | "Oops", "Something went wrong", "No data" | copy.md's state text |
| Motion | Fade-in or slide-up on load, staggered cards, count-up figures | Nothing moves on load |
| Motion | Cards that lift or scale on hover but do nothing on click | No hover effect |

## 4. Put a point of view back

A page scrubbed of tells but saying nothing particular is still generic, just quieter. Check
that it has:

- **The lead first**, with its comparison: the one number this reader acts on.
- **The data's own words** in titles and headings: the dataset's measure names, its markets,
  its periods, never stock dashboard vocabulary.
- **Asymmetry the content earns:** the wide span for the chart that explains the lead, a short
  list when the content is short, no symmetry chosen before the content.
- **In a creative look, one deliberate choice repeated** on every block (a typeface, a rule
  weight, a palette from the domain), not ten effects used once.

## 5. Final read

Ask: "What here would make a designer think a model built this?" Fix it, and ask again until
the answer is nothing. Then name the one thing a competitor's dashboard couldn't copy: if the
answer is the title, step 4 isn't done.

In a review, report each tell fixed as a row of the findings table (Should, or Blocking when it
misleads, like an "Insights" sentence no query measured), with Before, After and Why.
