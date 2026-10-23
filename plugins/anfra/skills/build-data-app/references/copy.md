# Words and numbers in a Data App

Read this with [page-design.md](page-design.md). Copy is part of the design: a page with a
generic title, raw numbers and "Something went wrong" reads as generated however good its layout.

## Names

- **`<title>` and `h1`: what the page is about, in the reader's words, 1 to 4 words, sentence
  case.** "Hotel performance", "Support service levels". Never "Dashboard", "Overview",
  "Analytics", an emoji or Title Case. The `<title>` is also the app's name in the host's list
  and the file name of its pictures, so keep `createApp({ title })` equal to it.
- **Card heading: what is plotted, and against what.** "RevPAR by hotel, vs last year",
  "Abandon rate by week". A plain label, never a question, never "Chart 1", "Breakdown" or
  "Trends".
- **One name per concept, everywhere on the page.** If the KPI says "Abandon rate", the chart,
  table header, tooltip and footer say "Abandon rate", not "Abandonment" or "Abandoned %". Take
  the name from the field or metric label (`anfra show`), in sentence case, and reuse it through
  one label function.
- **The reader's words, not the model's:** "Days past due", not `dpd30_rate`; "Hotel", not
  `property_id`. Never show a field name, a query name or an alias.

## The caption and the lead

- **The header caption states scope and basis**, and changes with the controls: "Year to date,
  compared with the same dates last year", "Weeks of Sep 1 to Oct 19, 2026, all channels".
- **A computed sentence states only what a query measured.** No judging adjectives ("strong",
  "significant", "worrying") and no causes ("driven by APAC") unless a query measured that share.
  "Revenue is up 6.2%; APAC is 41% of the increase" is a measurement; "Strong growth driven by
  APAC" is an opinion the page can't back.
- **A lead sentence is computed, never typed.** When the page has a hero sentence, build it from
  the query result with a template and branch on the data: "RevPAR is up 6.2% on last year;
  Lotus Bay is the only hotel behind". A finding written as static text is wrong the day the data
  changes. When the data does not support a clear sentence, show the figure alone.
- **Captions under a heading only add what the heading lacks** (the date basis, a filter in
  force, how a rate is defined). A caption that restates the heading gets deleted.

## Numbers

Format with `Intl.NumberFormat` and `Intl.DateTimeFormat`, from the raw string (api.md, Result
shape). One formatter per kind of number, defined once and reused.

| Kind | Format | Example |
| --- | --- | --- |
| Money in a figure or axis | compact past 10,000, at most 1 decimal | $1.2M, $84.5K |
| Money in a table or tooltip | full, no decimals past 100 | $1,234,567 |
| Count | grouped, compact past 100,000 on axes | 12,480 |
| Rate | percent, 1 decimal | 4.7% |
| Change of a rate | percentage points | +1.2 pts |
| Change of an amount or count | percent in a KPI delta pill; value and percent in tables and tooltips | +2.3%; +$817K (+2.3%) |
| Duration | the largest unit that keeps it whole | 42 s, 3 min 10 s, 2.4 h |
| Date | short month, never ISO; one locale for the whole page | Oct 19, 2026, Oct 2026, Week of Oct 13 |

- **Currency and locale come from the data.** Take the currency from the measure's AML `format`
  or label (`[$$]#,###0` is dollars); when neither says, ask. One locale for the whole page.
- **The SDK doesn't carry `format`:** not in `Anfra.datasets`, the result's columns or lineage.
  Read each measure's `format` from its AML when you write the app, and put it in the file as one
  map from the query alias to its formatter (`const formatOf = { revenue: money, orders: count }`),
  with a comment naming the AML it came from. Never guess a currency from a field name; a review
  keeps the map and fixes only a wrong entry.
- **Every number carries its unit**, joined by a non-breaking space where the unit is a word
  ("42&nbsp;s", "12&nbsp;agents").
- **Every change carries its comparison:** "+2.3% vs last year", never a bare "+2.3%".
- **Minus is the true minus (−, U+2212).** Round before you choose the sign, so "−0.0" never
  shows; zero change is "0" or "No change".
- **One precision per measure on the page.** If the KPI says 4.7%, the table says 4.7%, not 4.68%.
- **`null` is "No data"** (or an en dash in a table cell), never 0: zero and missing mean different
  things.

## States

| State | Write | Never |
| --- | --- | --- |
| Loading | Nothing: the skeleton says it. A label, if any, is "Loading…" | A spinner with "Please wait" |
| Empty | The scope that is empty, and how to widen it: "No calls on chat in this period. Choose a longer period or another channel." | "No data", "No results found", a bare 0 |
| Error | What failed, by block: "Couldn't load revenue by region", then `error.message` in caption size. A lost connection: "Couldn't reach the server", with "Try again" | "Something went wrong", "Oops", "Error 500", a query name |
| No access (`PermissionError`) | "You don't have access to this data" | An error styling |

## Footer

A definition list, then one basis line (layout in page-design.md, section 2):

- each measure on the page as a term and one clause from its AML description, in plain words, at
  most 15 words, no end period: "Service level" / "Share of calls answered within the queue's
  target time";
- one line for the comparison, the date basis ("by stay date", "by the week the ticket was
  opened") and the data's as-of date;
- any rounding or exclusion a reader needs to trust a number, in the entry of the measure it
  affects, not in a general note.

## Style

- **One-sentence UI text has no end period; two or more sentences keep all of theirs.**
- **No "please", no exclamation marks, no "Oops".** The page is a tool, not a host.
- **Real marks in UI text:** ’ in contractions (Couldn’t, don’t), “ ” around quoted words, … for
  an ellipsis, never three periods. Straight quotes belong to code.
- **No `·` chains of three or more parts.** Two-part labels are fine ("Lotus Bay · Deluxe");
  three or more become a sentence.
- **Dates and periods name the window:** "Week of Oct 13", "Jun to Aug average". Say "today" only
  when the data is live; for a snapshot, name its date.
