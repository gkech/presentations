# The Anatomy of a PostgreSQL Backup CRD: pgBackRest, Multi-Repo, and Tiered Storage

**PostgreSQL on Kubernetes Summit**, PGConf.EU 2026, Valencia
20–23 October 2026 · Audit 2 · the summit runs 13:30–16:45
This slot: **15:25 to 15:35** (10 minutes, no Q&A buffer)
George Kechagias, Senior Software Engineer @ Percona

[Event page](https://2026.pgconf.eu/community-events/pg_on_kubernetes/)

A 10-minute cut of the 40-minute abstract in
`../percona-live-2026-bay-area-slides/pgconf-valencia-abstracts.md` (#2). The long version's
PITR deep-dive, retention maths, and demo are dropped; what survives is one arc:
**engine → CRD → storage**.

## Files

- `slides.md` is the deck. Marp markdown: `---` between slides, `<!-- _class: … -->` picks the
  master, HTML comments are speaker notes. Each note opens with a `Land it:` line, the one
  sentence that slide has to deliver.
- `images/` holds the source logos. The one the deck actually uses (`percona-mark.png`) is
  **inlined** as a base64 data URI in the front matter, so the deck renders identically in the
  VS Code preview, on marp.app, and from the CLI with no local-file access.
- `.marprc.yml` is one line, `html: true`. The data-path diagram is inline `<svg>`, and Marp
  escapes HTML to literal text without it.
- `.vscode/settings.json` carries the same switch for the VS Code preview
  (`markdown.marp.html: "all"`; it is a string enum, not a boolean).

## Building

```sh
npx @marp-team/marp-cli@latest --no-stdin slides.md -o slides.pdf     # speaker PDF
npx @marp-team/marp-cli@latest --no-stdin slides.md -o slides.html    # self-contained
npx @marp-team/marp-cli@latest -s .                                   # live preview
```

`--no-stdin` only matters when you build from a script: marp-cli will otherwise read whatever is
on stdin instead of the file, and exit successfully having written nothing.

On marp.app, or any renderer that will not read `.marprc.yml`, pass `--html` or the diagram
renders as raw markup.

## Theme

PostgreSQL palette, Percona mark bottom-right of every slide.

| Token | Value | Used for |
| --- | --- | --- |
| `--pg` | `#336791` | PostgreSQL elephant blue: rules, bullets, diagram strokes |
| `--pg-deep` | `#1f3b52` | headings, dark masters, code-block ground |
| `--paper` | `#f7f9fb` | light slide background |
| `--amber` | `#cf8a00` | the takeaway callout, dark-master rule, "watch out" strokes |

Masters: *(none)* light content · `dark` (beat change) · `title` · `lead` (one big statement) ·
`dense` (shrinks type for code- or table-heavy slides).

Three things worth knowing before editing:

- **Never put a blank line inside a `<figure>`.** markdown-it ends the HTML block there and
  parses the rest as markdown, so half the diagram renders as escaped text.
- Marp ships highlight.js with a **light-background** palette, which is navy-on-navy against the
  dark `pre`. The front matter resets every `.hljs-*` token and re-tints a few; if you add a new
  language, check the colours before trusting it.
- **No em dashes anywhere in the copy.** Use a colon, a comma, parentheses, or two sentences.

## Running order and budget

10 minutes, 13 slides. The three beats get roughly three minutes each; slide 8 is the one the
talk exists for, so pause there. Timings live here and in the speaker notes, never on a slide.

| # | Slide | Beat | Mark |
| --- | --- | --- | --- |
| 1 | Title | | 0:00 |
| 2 | `kubectl apply -f backup.yaml` | setup | 0:30 |
| 3 | pgBackRest | engine | 1:15 |
| 4 | How it attaches to PostgreSQL | engine | 2:15 |
| 5 | All of that has one field behind it *(divider)* | | 4:00 |
| 6 | `spec.backups.pgbackrest.repos[]` | CRD | 4:10 |
| 7 | One backup, one object | CRD | 5:20 |
| 8 | **Multi-repo is not redundancy** | storage | 6:30 |
| 9 | Tiering four repos | storage | 7:00 |
| 10 | Bootstrap a new cluster from an existing repo | storage | 8:10 |
| 11 | Where the abstraction leaks | honesty | 8:50 |
| 12 | What I would want you to leave with | close | 9:30 |
| 13 | Thank you | | 9:50 |

If you are running long, slide 10 is the cut: it is the only one nothing else depends on. Do
**not** cut 11; it is what earns the hallway conversation.

## Sources

Everything in the deck is read off the operator source rather than the docs:

- `github.com/percona/percona-postgresql-operator`, specifically `pkg/apis/pgv2.percona.com/v2`
  (`PerconaPGBackup`, `PerconaPGRestore`, `Backups`), `internal/pgbackrest/`
  (`postgres.go` for `archive_command` and `restore_command`, `config.go` for the stanza name),
  and `internal/controller/postgrescluster/pgbackrest.go` (the repo-host StatefulSet).
- `pkg/apis/postgres-operator.crunchydata.com/v1beta1/pgbackrest_types.go` for `PGBackRestRepo`
  (the `volume | s3 | gcs | azure` union), `PGBackRestBackupSchedules`, and `PGBackRestDataSource`.
- The `^repo[1-4]$` cap is a kubebuilder pattern in both API groups. It is the operator's own
  limit, not the engine's: pgBackRest allows indexed option keys 1 to 256
  (`CFG_OPTION_KEY_MAX` in `src/config/parse.c`), and the user guide's own worked example
  configures `repo5`.
- `pgbackrest.org/user-guide.html` for `archive-async`, `archive-push-queue-max`, and retention.
