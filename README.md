# Enrico Wegner — personal website

A static academic website built with [Quarto](https://quarto.org) (≥ 1.9). Three
pages — **Home**, **Research**, **Open Source** — reproducing a design authored in
Claude Design, rebuilt to be edited without touching HTML or CSS.

```
quarto preview     # live-reloading dev server
quarto render      # build the static site into _site/
```

---

## Editing content

Everything you'll normally change lives in a handful of plain-text files.

| You want to change… | Edit this file |
|---|---|
| Hero text, intro paragraphs, action buttons, hero profile icons | [`index.qmd`](index.qmd) |
| Upcoming talks | [`index.qmd`](index.qmd) (the `.upcoming-talks` block — see below) |
| The "explained on 3 levels" paragraphs | [`index.qmd`](index.qmd) (the `.panel-tabset` block) |
| The Job Market Paper card (shown on Home **and** Research) | [`_partials/jmp-card.qmd`](_partials/jmp-card.qmd) — one source, both pages |
| Research page intro / section order | [`research.qmd`](research.qmd) |
| Open Source page intro | [`open-source.qmd`](open-source.qmd) |
| Navbar / footer links, footer profile icons, site title | [`_quarto.yml`](_quarto.yml) |
| **Colours, fonts, accent, spacing** | [`scss/_tokens.scss`](scss/_tokens.scss) |

Markdown basics: `**bold**`, `[link text](https://…)`. A button is just a link with a
class: `[Label](https://…){.btn-accent}` (solid) or `{.btn-outline}` (bordered).

In the "explained on 3 levels" callout, a **Continue to <next level> →** link is added
automatically at the end of every level except the last — it switches to the next tab
and scrolls to the top of the text. It's generated from the tab names, so just edit the
level paragraphs; nothing to maintain by hand.

### Upcoming talks

The home page shows a minimal **Upcoming Talks** list above the job-market paper. It looks
after itself: past talks are dropped automatically (on the visitor's own clock) and the
whole list is hidden when nothing is upcoming — so you can leave old entries in place, or
clear them out, without the page ever showing a stale or empty list.

To add a talk, copy one `.talk` block inside the `.upcoming-talks` section of
[`index.qmd`](index.qmd):

```markdown
::: {.talk data-date="2026-09-15" data-until="2026-09-17"}
[Conference or seminar name]{.talk-name}
[15–17 September 2026]{.talk-when}
:::
```

- `data-date` — the **start** date in ISO form (`YYYY-MM-DD`). Used to order the list.
- `data-until` — the **last day** the talk should show, ISO form. Optional; omit it for a
  single-day event (it then defaults to `data-date`). The talk disappears the day *after*
  this date.
- `.talk-name` / `.talk-when` — what's displayed. `.talk-when` is free text, so write the
  date however you like (e.g. `25 August 2026`).
- To **link** an event to its webpage, make the name a link — it renders with a subtle
  underline (a talk with no link stays plain text):
  `[EEA-ESEM Congress](https://www.eea-esem-congresses.org/){.talk-name}`.

Ordering is automatic (soonest first), so entries don't need to be in order. The card is
revealed by a small script ([`_partials/talks.html`](_partials/talks.html)); with
JavaScript disabled it stays hidden.

---

## Adding a research output ("add one file")

Create a new `.qmd` in the matching folder — it appears automatically, sorted by date.

- Working paper → `research/working-papers/`
- Publication → `research/publications/`
- Work in progress → `research/work-in-progress/`

Copy an existing stub and change the fields. All fields are optional except `title`:

```yaml
---
title: "Paper title"
authors: "Lastname, Wegner & Coauthor"   # the meta line
venue: "Journal / Conference 2025"        # rendered in italics after the authors
date: "2025-03-01"                        # ISO date — controls sort order
doi: "10.1109/XXXX"                       # renders a "DOI: … →" link
description: "A full abstract, as long as you like."   # optional; long ones get a "Read more"
showFullAbstract: true                    # optional — always show the full abstract (see below)
embargo: true                             # shows the amber "under embargo" badge instead of a DOI
compact: true                             # smaller card (used for the OECD / policy reports)
placeholder: true                         # dimmed "coming soon" style
phd: true                                 # thesis chapter → PhD Research section (see below)
group: "OECD Statistics Working Papers"   # policy/statistical report (see below)
draft: true                               # hide this paper from its section (see below)
---
```

### How the Research page is laid out

The page (`research.qmd`) reads top to bottom as:

1. **PhD Research** — the Job Market Paper card (from
   [`_partials/jmp-card.qmd`](_partials/jmp-card.qmd)), then **Further Thesis
   Chapters** — every stub flagged `phd: true`, with its abstract.
2. **Working Papers** *(only shown if any exist)*
3. **Work in Progress** *(only shown if any exist)*
4. **Publications**
5. **Policy and Statistical Reports** — the OECD papers.

**Marking a thesis chapter — `phd: true`.** A chapter of the PhD is still authored
as an ordinary stub in `research/working-papers/` or `research/work-in-progress/`;
add `phd: true` and it moves up into the **PhD Research** section (under *Further
Thesis Chapters*) **and** is removed from the Working Papers / Work in Progress
section it would otherwise sit in. So each PhD paper appears once, in the PhD
section — nothing to duplicate or cross-link. Give it a `description` so its
abstract shows there.

**Working Papers / Work in Progress appear only when non-empty.** These two
sections (heading included) are hidden whenever they have no items — e.g. when
every working paper is a `phd: true` thesis chapter. They reveal automatically as
soon as a non-PhD stub is added to the matching folder. (Mechanism: the heading +
listing are wrapped in a `.research-section.is-optional` block that's hidden in CSS
and revealed by [`_partials/optional-sections.html`](_partials/optional-sections.html)
only when its listing rendered at least one item — the same progressive-enhancement
pattern as the upcoming-talks card.)

**Policy and Statistical Reports — `group: "OECD*"`.** This section is its own
listing filtered by `group`; give an item a `group` value (e.g. `"OECD Statistics
Working Papers"`) to place it there — and it's automatically excluded from the main
Publications list.

**Long abstracts / "Read more"** — by default a long `description` is clamped to a
few lines with a **Read more / Show less** toggle, keeping the page compact. Short
abstracts that already fit show in full with no button. Set `showFullAbstract: true`
on an item to always show its abstract in full (no clamp, no button); the default
(or `showFullAbstract: false`) uses the toggle. It works without JavaScript too —
then the full abstract is simply always shown.

**Hiding a paper** — set `draft: true` to remove it from its section without
deleting the file. This is Quarto's built-in draft flag: the item disappears from
its listing (and from search); flip it back to `false`, or remove the line, to show
it again. Works for open-source projects too.

## Adding an open-source project

Create a `.qmd` in `open-source/`:

```yaml
---
title: "PackageName.jl"
description: "One-line description."
link: "https://github.com/enweg/PackageName.jl"   # the card links here
status: active            # active | archived  → which grid it lands in
role: "contributor"       # optional badge
order: 6                  # position within its grid (lower = earlier)
draft: true               # hide this project from its grid (optional)
---
```

## Adding teaching material

The Open Source page has a **Teaching Material** section (after the software) for
open courses, notes, and class repos. Adding one is the same idea — create a `.qmd`
in `teaching/`:

```yaml
---
title: "Course or topic name"      # a readable title (shown in the sans face, not mono)
description: "One-line description of what it teaches."
link: "https://github.com/enweg/…" # the card links here
order: 5                           # position in the grid (lower = earlier)
---
```

Teaching cards reuse the package-card layout but render their titles in the normal
sans font (since they're course names, not code identifiers). `draft: true` hides
one, just like the other listings.

> Each research / open-source / teaching stub also generates a minimal standalone page
> (e.g. `open-source/tca-jl.html`). These are intentionally kept out of site search and
> the navbar (via the `_metadata.yml` in each folder); you can ignore them.

---

## Changing the accent colour

The whole site accent (links, buttons, badges, active nav) is one line in
[`scss/_tokens.scss`](scss/_tokens.scss). Three palettes are pre-named:

```scss
$accent:      $forest;        // swap for $navy or $burgundy
$accent-tint: $forest-tint;   // and the matching tint
```

That's the build-time equivalent of the original design's theme picker.

---

## Assets you need to supply

Two files are referenced but must be provided by you:

1. **CV** — the "Download CV" button links to `assets/cv-enrico-wegner.pdf`. Drop your
   compiled PDF there. (The design project only shipped the LaTeX source.)
2. **Portrait** — `assets/profile.jpg` is currently a neutral placeholder. Replace it
   with your headshot (roughly square, ~800 px wide is plenty; it's cropped to a
   rounded rectangle on desktop and a circle on mobile). The original portrait in the
   design project exceeded the design tool's 256 KiB transfer cap and couldn't be
   pulled automatically.

Fonts (Libre Franklin, IBM Plex Mono) are **self-hosted** in `assets/fonts/` — no
requests go to Google, which is friendlier for an EU audience.

---

## Building & deploying

```bash
quarto render                 # → _site/
```

Deploy `_site/` to any static host. For GitHub Pages:

```bash
quarto publish gh-pages       # first run sets up the gh-pages branch
```

Or add a GitHub Action (`quarto-dev/quarto-actions/publish`) to auto-publish on push
to `main`. `_site/` and `.quarto/` are git-ignored.

---

## How it's organised

- `scss/_tokens.scss` — **named** colours, type, radii, shadows (single source of truth;
  everything else references these, never raw hex).
- `scss/_fonts.scss` — self-hosted `@font-face` declarations.
- `scss/_components.scss` — navbar, hero, cards, badges, buttons, section headers.
- `scss/_tabset.scss` — the pill-style tabs for the 3-levels callout.
- `scss/_motion.scss` — animations. The page-entry movement is gated behind
  `prefers-reduced-motion`; the small job-market dot pulse is left on unconditionally
  to match the original design (it's a slow, gentle opacity pulse, not a strobe).
- `ejs/*.ejs` — templates that turn each stub's front matter into a card.

## Deviations from the original design

Minor, deliberate changes (fidelity vs. maintainability — see `Plan.md` §8):

- **Tokens live in SCSS, not `_brand.yml`.** Quarto 1.9 doesn't expose `_brand.yml`
  palette entries as SCSS variables, so `scss/_tokens.scss` is the single source of
  truth for colour/type.
- **Runtime theme picker → one-line build-time accent switch** (see above).
- **Fonts self-hosted** instead of loaded from Google.
- **Portrait is a placeholder** pending your headshot (see Assets).
- **One shared JMP abstract** (the design had two slightly different blurbs on Home vs
  Research; unified here).
- **Research items share one type scale** (the design varied a few sizes); the OECD
  sub-group still uses the smaller `compact` style.
