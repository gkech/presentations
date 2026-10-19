# The Storage Layer Behind Kubernetes Database Operators That Enables Real-World DBaaS

Percona Live 2026 Amsterdam — Kubernetes track
Friday, September 11 · 11:00–12:00 · Matterhorn 2
George Kechagias, Senior Software Engineer @ Percona

[Talk page](https://perconalive.com/2026-amsterdam/talks/the-storage-layer-behind-kubernetes-database-operators-that-enables-re/)

## Files

- `slides.md` — the deck. Marp markdown: `---` between slides, `<!-- _class: … -->` picks the
  master, HTML comments are speaker notes.
- `assets/` — the source masters (rendered from `Percona Live Amsterdam Slide Template.pdf`) and
  the Poppins subsets. They are **inlined** into `slides.md`'s front matter, so the deck needs no
  local-file access, no theme registration and no config: it looks the same in the VS Code
  preview, on marp.app and from the CLI.
- `notes/` — research, links, and the earlier outline of this talk.
- `.marprc.yml` — one line, `html: true`. The diagrams are inline SVG, and Marp escapes
  `<svg>` to literal text without it.
- `.vscode/settings.json` — the same switch for the VS Code preview
  (`markdown.marp.html`), so the preview matches the CLI.

## Building

```sh
marp slides.md -o deck.pdf          # or: npx @marp-team/marp-cli@latest slides.md -o deck.pdf
marp slides.md -o deck.html         # self-contained, opens anywhere
marp slides.md --pptx -o deck.pptx  # if it has to end up in Google Slides
marp -s .                           # live preview server while writing
```

Everything the deck needs is in its own front matter, apart from one switch: `html: true`,
which lives in `.marprc.yml` so the commands above need no flags. On marp.app, or any
renderer that won't read that file, pass `--html` or the diagrams will render as raw markup.

Why it's needed: the diagrams are inline `<svg>`, and Marp escapes HTML by default. Inlining
them as `data:image/svg+xml` images is *not* an alternative — markdown-it's link validator
rejects that scheme, and the image silently renders as literal text.

Three things worth knowing before you edit the style block:

- An inline `style:` is layered *on top of* Marp's built-in theme rather than replacing it, so the
  built-in colours have to be overridden explicitly. There's a block that does exactly that.
- The built-in theme centres content with `place-content: safe center center`. Only
  `place-content` overrides it — `display: block` does not.
- Relative `url()` paths in a style block do **not** resolve in the VS Code preview webview, which
  is why the images are inlined rather than referenced.

## Writing slides

| You want | You write |
| --- | --- |
| Default light slide | nothing — it's the default |
| Charcoal slide, for a beat change | `<!-- _class: dark -->` |
| Section divider | `<!-- _class: section -->` + `# Title` + a time line |
| The takeaway line | a `Land it:` line at the top of the speaker notes — not on the slide |
| Speaker notes | an HTML comment |
| A slide that's too full | `<!-- _class: dense -->` (6+ bullets shrink automatically) |
| A diagram under the bullets | a `<figure><svg class="d" viewBox="0 0 1014 …">` block |
| A diagram beside the bullets | `<!-- _class: aside -->` + a `<figure>` (it's positioned for you) |
| One big statement, no bullets | `<!-- _class: hero -->` |
| A label above the heading | `<p class="kicker">Act three · 0:10</p>` |
| A row of big figures | `<div class="stats">` |

Style rules baked into the theme: charcoal `#282727`, white, violet `#653DF4` for the takeaway
rule and bullets, lime `#F6FE54` on dark slides. Six lines per slide is the ceiling — a seventh
means it's two slides. Never two dark slides in a row; the master is punctuation, not a mode.
The logo and `© 2026 Percona` are part of every master, so don't re-add them.

### Diagrams

Inline SVG, hand-written, no build step. Three rules:

- **Never put a blank line inside a `<figure>`.** markdown-it ends the HTML block there and
  parses the rest as markdown, so half your diagram renders as escaped text.
- **Never hard-code a colour.** Paint with `var(--ink)`, `--accent`, `--panel`, `--edge`,
  `--dim`, or the `.box` / `.box-a` / `.ln` / `.ln-a` / `.acc` classes. They're re-pointed
  per master, so one diagram is correct on both the light and charcoal slides.
- **Author against the safe zone.** `viewBox="0 0 1014 …"` on a light slide, `0 0 948 …` on
  a dark one, `0 0 462 …` for an `.aside` figure. Height around 250 leaves room for the
  takeaway line.

To regenerate the backgrounds after a template change:

```sh
pdftoppm -r 128 -jpeg -jpegopt quality=72 "Percona Live Amsterdam Slide Template.pdf" assets/m
# m-1 → dark, m-2 → title, m-3 → light; base64 each into the :root block at the
# end of the front matter (--bg-dark / --bg-title / --bg-light)
```

## Budget

50 min content + 10 min Q&A. 44 slides across 6 sections, budgeted to land the last content
slide on 0:50. The deck runs four beats — what you configure, what the operator does, what
Kubernetes does, and what to do when the storage is slow. The performance section gets 18 min;
the first four sections exist to give it vocabulary.

`notes/slides-v1-skeleton.md` is the earlier lifecycle-shaped outline, kept for reference.
