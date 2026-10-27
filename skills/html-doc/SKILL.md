---
name: html-doc
description: >-
  Produce a rich, self-contained HTML document instead of Markdown for any deliverable
  meant to be READ or SHARED rather than executed (specs, plans, design docs, reviews,
  reports, explainers, postmortems, status updates, comparisons). Use this whenever you
  are about to write a .md artifact for the user to look at — even if they never say
  "HTML" — treating "write me a doc / spec / report / summary / review" as a request for
  one. Do NOT use it for repo Markdown that must stay .md (README, CHANGELOG, CLAUDE.md,
  docs/*.md, issue templates) or for short answers that belong inline in chat.
---

# Rich HTML Documents

When the user wants a document to read or share, write a single self-contained HTML
file instead of Markdown. HTML carries more than Markdown can — real tables, inline
SVG, annotated code, color, layout, and interaction — so the doc is denser, more
navigable, and actually gets read.

## Output requirements

- **One self-contained `.html` file.** All CSS and JS inline. No external requests, CDN
  links, fonts, or build step — the user double-clicks the file and it renders fully
  offline. This is what makes it shareable.
- **Save it where the user expects** — next to the work it describes, named after the
  document (e.g. `auth-redesign-plan.html`, `rate-limiter-explainer.html`).
- **State the path** and one line on what's inside when you're done.

## Use the medium — don't transcribe Markdown into `<p>` tags

Design the page yourself to fit the content; there is no fixed structure to follow.
Reach for these wherever they earn their place:

- **Diagrams → inline SVG.** Anywhere you'd reach for ASCII art — flows, architecture,
  sequences, state machines — draw it as `<svg>`. This is the biggest upgrade over Markdown.
- **Tabular data → real `<table>`s.** Comparisons, option matrices, config, metrics, tradeoffs.
- **Code → annotated blocks.** Filename bars, highlighted lines, inline notes; for reviews,
  render the diff and color-code findings by severity.
- **Callouts** for warnings, gotchas, assumptions, and key takeaways so they don't get lost.
- **Navigation** for anything long — a table of contents, tabs, collapsible sections.
- **A metadata header** so the reader is oriented: title, one-line summary, date, doc type.
- **Responsive + light/dark** so it reads on a phone and a laptop.
- **`@media print` rules** if the doc might be exported to PDF — shrink oversized hero
  padding and add `page-break-inside: avoid` so diagrams/cards don't split across pages.

## Interactivity — when the doc is about choosing or tuning

If the document exists to help decide or explore (parameters, tradeoffs, prioritization),
make it interactive rather than static — sliders, toggles, draggable cards, tabs to compare
options, side-by-side editors with live preview. Vanilla JS only, kept inline.

**Always give an interactive doc an export** — a "Copy as Markdown / JSON / prompt" button
that turns what the user did in the UI back into text they can paste or commit. The
interaction is only useful if its result can flow back into the loop.

## PDF export — when the user asks for a PDF

Render the HTML through headless Chromium — the same engine that drew the page, so
fonts, backgrounds, and SVG transfer faithfully. **Prefer Playwright's `page.pdf()`**
over the Chrome CLI; it gives real control over margins and print-media emulation.

```js
await page.goto('file:///abs/path/to/doc.html', { waitUntil: 'networkidle' });
await page.emulateMedia({ media: 'print' });   // turns on the @media print rules
await page.pdf({
  path: '/abs/path/to/doc.pdf',
  format: 'Letter',
  printBackground: true,                        // keeps hero/callout background colors
  margin: { top: '0.5in', bottom: '0.5in', left: '0.55in', right: '0.55in' },
});
```

The three levers that make it look good instead of default-ugly:

1. **`printBackground: true`** — the single biggest one. Without it Chrome drops all
   background colors (hero bands, colored callout fills go white).
2. **`emulateMedia({ media: 'print' })`** — activates the `@media print` block so hero
   padding shrinks and `page-break-inside: avoid` keeps blocks from splitting.
3. **Embedded fonts** — because the HTML is self-contained, base64'd fonts render
   identically anywhere; a plain browser "Print → Save as PDF" would substitute generic ones.

Quick CLI fallback (less control over margins / print emulation):

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --print-to-pdf="out.pdf" --no-pdf-header-footer \
  "file:///abs/path/to/doc.html"
```

Offer to drop a tiny `make-pdf.sh` in the folder so regenerating after edits is one command.
