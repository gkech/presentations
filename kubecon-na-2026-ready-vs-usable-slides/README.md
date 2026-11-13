# Closing the Gap Between Ready and Usable in a DBaaS Platform

**Platform Engineering TCG booth**, KubeCon + CloudNativeCon NA 2026, Salt Lake City
Tuesday 10 November 2026 · Solutions Showcase, Hall 5, Cosmic Canyon Zone · Kiosk P-16B
Lightning talks run 4:00 to 5:00 PM (10 minutes each)
George Kechagias, Senior Software Engineer @ Percona

The booth has a screen and an HDMI cable. Bring the laptop, a USB-C adapter, and a charger.

## Abstract

An engineer asks for a Postgres database through a web portal, CLI, or an AI agent calling the
same API. Two minutes later the status flips to Ready. They take the connection string, deploy
the app, and it can't connect.

Nothing on the platform side failed. The pod is Running, the probes are green, the operator
condition says Available. What the engineer actually hit was the connection pooler not accepting
traffic yet, or the role's password not yet landed in the secret the app mounts, or a TLS cert
still being issued. Either way, they're now debugging infrastructure they were promised they'd
never have to look at.

This lightning talk is about Ready as a promise the platform makes. Using Postgres as a service,
I'll walk through where the usual signals go green ahead of the only thing the engineer cares
about, why that gap gets worse when an agent reads the status and moves on with no human around
to notice, and how the platform can absorb it instead, providing status that reflects what the
engineer can actually do.

## The deck

11 slides for a 10-minute slot, written for platform engineers who build an internal DBaaS on
top of operators:

1. Title
2. `kubectl get pg` says `ready`, and the app can't connect
3. Ready gets copied up the stack: pods, operator CR, platform API, consumer
4. What one operator's `ready` measures (Percona Operator for PostgreSQL)
5. Four things `ready` never looks at: the role's password, pooler auth, the cert, the network path
6. Agents as consumers of the platform API
7. Closing the gap in the platform layer: wait for what you can see, then test the connection
8. A status an agent can act on
9. Time to first query versus time to Ready
10. Takeaways
11. Thank you

The operator facts come from `percona/percona-postgresql-operator` at `52d3dc7`
(5 November 2026). The fixes on slides 7 and 8 are a proposal, and slide 7's notes say so. Speaker
notes are the HTML comments, a few lines each. On slides 2 to 10 they open with a `Land it:` line,
the one point that slide has to make.

## Files

- `slides.md` is the source. The look follows the Percona Live 2026 Amsterdam deck (charcoal,
  violet `#653df4`, lime `#f6fe54`, Poppins, diagonal wedges, the violet play mark), with the
  wedges drawn in CSS instead of the event's background images. The Percona Live lockup and the
  mountain photo are left out; the plain Percona logo replaces them.
  Classes: (none) light content, `dark`, `section`, `title`, `dense`, `lead`.
- `slides.pdf` and `slides.html` are builds of it. Every image and font is inlined in the front
  matter as a data URI, so both are self-contained.
- `.marprc.yml` is one line, `html: true`. The stack diagram is inline SVG, and Marp escapes it
  to literal text without that switch.
- `images/` holds the sources for what is inlined:
  - `kcna26-white.svg` from the KubeCon NA 2026 event page, and `kcna26-white.png`, the same
    logo rasterised. The deck uses the PNG.
  - `cnpe-mark.png`, cropped from the community banner at cloudnativeplatforms.com
    (`cnpe-community-banner.png`).
  - `percona-mark.png`, the one-colour Percona logo, inverted to white in CSS.
  - `play.png`, the violet play mark.
  - `fonts/`, the Poppins 300 and 600 subsets.

## Building

```sh
npx @marp-team/marp-cli@latest --no-stdin slides.md -o slides.pdf
npx @marp-team/marp-cli@latest --no-stdin slides.md -o slides.html
npx @marp-team/marp-cli@latest -s .
```

Rebuild both after every edit to `slides.md`; they are checked in.
