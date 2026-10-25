---
marp: true
paginate: true
header: "PostgreSQL on Kubernetes Summit · PGConf.EU 2026, Valencia"
footer: ""
style: |
  /*
   * PostgreSQL-flavoured theme. Palette taken from the PostgreSQL brand:
   * elephant blue #336791, deep slate #1f3b52, paper #f7f9fb, amber #cf8a00.
   * Layered on top of Marp's default theme, so built-in colours are overridden
   * explicitly where they would otherwise win.
   *
   * Classes: (none) light content | dark | title | lead | dense
   */

  :root {
    --pg: #336791;
    --pg-deep: #1f3b52;
    --paper: #f7f9fb;
    --ink: #16222c;
    --amber: #cf8a00;
    --rule: #d6e0e8;
    --dim: #5a7183;
    --logo: url('data:image/png;base64,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');
    --pglogo: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIj8+DQo8IURPQ1RZUEUgc3ZnIFBVQkxJQyAiLS8vVzNDLy9EVEQgU1ZHIDEuMS8vRU4iDQogICJodHRwOi8vd3d3LnczLm9yZy9HcmFwaGljcy9TVkcvMS4xL0RURC9zdmcxMS5kdGQiPg0KDQo8c3ZnIHdpZHRoPSI0MzIuMDcxcHQiIGhlaWdodD0iNDQ1LjM4M3B0IiB2aWV3Qm94PSIwIDAgNDMyLjA3MSA0NDUuMzgzIiB4bWw6c3BhY2U9InByZXNlcnZlIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciPg0KPGcgaWQ9Im9yZ2luYWwiIHN0eWxlPSJmaWxsLXJ1bGU6bm9uemVybztjbGlwLXJ1bGU6bm9uemVybztzdHJva2U6IzAwMDAwMDtzdHJva2UtbWl0ZXJsaW1pdDo0OyI+DQoJPC9nPg0KPGcgaWQ9IkxheWVyX3gwMDIwXzMiIHN0eWxlPSJmaWxsLXJ1bGU6bm9uemVybztjbGlwLXJ1bGU6bm9uemVybztmaWxsOm5vbmU7c3Ryb2tlOiNGRkZGRkY7c3Ryb2tlLXdpZHRoOjEyLjQ2NTE7c3Ryb2tlLWxpbmVjYXA6cm91bmQ7c3Ryb2tlLWxpbmVqb2luOnJvdW5kO3N0cm9rZS1taXRlcmxpbWl0OjQ7Ij4NCjxwYXRoIHN0eWxlPSJmaWxsOiMwMDAwMDA7c3Ryb2tlOiMwMDAwMDA7c3Ryb2tlLXdpZHRoOjM3LjM5NTM7c3Ryb2tlLWxpbmVjYXA6YnV0dDtzdHJva2UtbGluZWpvaW46bWl0ZXI7IiBkPSJNMzIzLjIwNSwzMjQuMjI3YzIuODMzLTIzLjYwMSwxLjk4NC0yNy4wNjIsMTkuNTYzLTIzLjIzOWw0LjQ2MywwLjM5MmMxMy41MTcsMC42MTUsMzEuMTk5LTIuMTc0LDQxLjU4Ny03YzIyLjM2Mi0xMC4zNzYsMzUuNjIyLTI3LjcsMTMuNTcyLTIzLjE0OGMtNTAuMjk3LDEwLjM3Ni01My43NTUtNi42NTUtNTMuNzU1LTYuNjU1YzUzLjExMS03OC44MDMsNzUuMzEzLTE3OC44MzYsNTYuMTQ5LTIwMy4zMjIgICAgQzM1Mi41MTQtNS41MzQsMjYyLjAzNiwyNi4wNDksMjYwLjUyMiwyNi44NjlsLTAuNDgyLDAuMDg5Yy05LjkzOC0yLjA2Mi0yMS4wNi0zLjI5NC0zMy41NTQtMy40OTZjLTIyLjc2MS0wLjM3NC00MC4wMzIsNS45NjctNTMuMTMzLDE1LjkwNGMwLDAtMTYxLjQwOC02Ni40OTgtMTUzLjg5OSw4My42MjhjMS41OTcsMzEuOTM2LDQ1Ljc3NywyNDEuNjU1LDk4LjQ3LDE3OC4zMSAgICBjMTkuMjU5LTIzLjE2MywzNy44NzEtNDIuNzQ4LDM3Ljg3MS00Mi43NDhjOS4yNDIsNi4xNCwyMC4zMDcsOS4yNzIsMzEuOTEyLDguMTQ3bDAuODk3LTAuNzY1Yy0wLjI4MSwyLjg3Ni0wLjE1Nyw1LjY4OSwwLjM1OSw5LjAxOWMtMTMuNTcyLDE1LjE2Ny05LjU4NCwxNy44My0zNi43MjMsMjMuNDE2Yy0yNy40NTcsNS42NTktMTEuMzI2LDE1LjczNC0wLjc5NywxOC4zNjdjMTIuNzY4LDMuMTkzLDQyLjMwNSw3LjcxNiw2Mi4yNjgtMjAuMjI0ICAgIGwtMC43OTUsMy4xODhjNS4zMjUsNC4yNiw0Ljk2NSwzMC42MTksNS43Miw0OS40NTJjMC43NTYsMTguODM0LDIuMDE3LDM2LjQwOSw1Ljg1Niw0Ni43NzFjMy44MzksMTAuMzYsOC4zNjksMzcuMDUsNDQuMDM2LDI5LjQwNmMyOS44MDktNi4zODgsNTIuNi0xNS41ODIsNTQuNjc3LTEwMS4xMDciLz4NCjxwYXRoIHN0eWxlPSJmaWxsOiMzMzY3OTE7c3Ryb2tlOm5vbmU7IiBkPSJNNDAyLjM5NSwyNzEuMjNjLTUwLjMwMiwxMC4zNzYtNTMuNzYtNi42NTUtNTMuNzYtNi42NTVjNTMuMTExLTc4LjgwOCw3NS4zMTMtMTc4Ljg0Myw1Ni4xNTMtMjAzLjMyNmMtNTIuMjctNjYuNzg1LTE0Mi43NTItMzUuMi0xNDQuMjYyLTM0LjM4bC0wLjQ4NiwwLjA4N2MtOS45MzgtMi4wNjMtMjEuMDYtMy4yOTItMzMuNTYtMy40OTZjLTIyLjc2MS0wLjM3My00MC4wMjYsNS45NjctNTMuMTI3LDE1LjkwMiAgICBjMCwwLTE2MS40MTEtNjYuNDk1LTE1My45MDQsODMuNjNjMS41OTcsMzEuOTM4LDQ1Ljc3NiwyNDEuNjU3LDk4LjQ3MSwxNzguMzEyYzE5LjI2LTIzLjE2MywzNy44NjktNDIuNzQ4LDM3Ljg2OS00Mi43NDhjOS4yNDMsNi4xNCwyMC4zMDgsOS4yNzIsMzEuOTA4LDguMTQ3bDAuOTAxLTAuNzY1Yy0wLjI4LDIuODc2LTAuMTUyLDUuNjg5LDAuMzYxLDkuMDE5Yy0xMy41NzUsMTUuMTY3LTkuNTg2LDE3LjgzLTM2LjcyMywyMy40MTYgICAgYy0yNy40NTksNS42NTktMTEuMzI4LDE1LjczNC0wLjc5NiwxOC4zNjdjMTIuNzY4LDMuMTkzLDQyLjMwNyw3LjcxNiw2Mi4yNjYtMjAuMjI0bC0wLjc5NiwzLjE4OGM1LjMxOSw0LjI2LDkuMDU0LDI3LjcxMSw4LjQyOCw0OC45NjljLTAuNjI2LDIxLjI1OS0xLjA0NCwzNS44NTQsMy4xNDcsNDcuMjU0YzQuMTkxLDExLjQsOC4zNjgsMzcuMDUsNDQuMDQyLDI5LjQwNmMyOS44MDktNi4zODgsNDUuMjU2LTIyLjk0Miw0Ny40MDUtNTAuNTU1ICAgIGMxLjUyNS0xOS42MzEsNC45NzYtMTYuNzI5LDUuMTk0LTM0LjI4bDIuNzY4LTguMzA5YzMuMTkyLTI2LjYxMSwwLjUwNy0zNS4xOTYsMTguODcyLTMxLjIwM2w0LjQ2MywwLjM5MmMxMy41MTcsMC42MTUsMzEuMjA4LTIuMTc0LDQxLjU5MS03YzIyLjM1OC0xMC4zNzYsMzUuNjE4LTI3LjcsMTMuNTczLTIzLjE0OHoiLz4NCjxwYXRoIGQ9Ik0yMTUuODY2LDI4Ni40ODRjLTEuMzg1LDQ5LjUxNiwwLjM0OCw5OS4zNzcsNS4xOTMsMTExLjQ5NWM0Ljg0OCwxMi4xMTgsMTUuMjIzLDM1LjY4OCw1MC45LDI4LjA0NWMyOS44MDYtNi4zOSw0MC42NTEtMTguNzU2LDQ1LjM1Ny00Ni4wNTFjMy40NjYtMjAuMDgyLDEwLjE0OC03NS44NTQsMTEuMDA1LTg3LjI4MSIvPg0KPHBhdGggZD0iTTE3My4xMDQsMzguMjU2YzAsMC0xNjEuNTIxLTY2LjAxNi0xNTQuMDEyLDg0LjEwOWMxLjU5NywzMS45MzgsNDUuNzc5LDI0MS42NjQsOTguNDczLDE3OC4zMTZjMTkuMjU2LTIzLjE2NiwzNi42NzEtNDEuMzM1LDM2LjY3MS00MS4zMzUiLz4NCjxwYXRoIGQ9Ik0yNjAuMzQ5LDI2LjIwN2MtNS41OTEsMS43NTMsODkuODQ4LTM0Ljg4OSwxNDQuMDg3LDM0LjQxN2MxOS4xNTksMjQuNDg0LTMuMDQzLDEyNC41MTktNTYuMTUzLDIwMy4zMjkiLz4NCjxwYXRoIHN0eWxlPSJzdHJva2UtbGluZWpvaW46YmV2ZWw7IiBkPSJNMzQ4LjI4MiwyNjMuOTUzYzAsMCwzLjQ2MSwxNy4wMzYsNTMuNzY0LDYuNjUzYzIyLjA0LTQuNTUyLDguNzc2LDEyLjc3NC0xMy41NzcsMjMuMTU1Yy0xOC4zNDUsOC41MTQtNTkuNDc0LDEwLjY5Ni02MC4xNDYtMS4wNjljLTEuNzI5LTMwLjM1NSwyMS42NDctMjEuMTMzLDE5Ljk2LTI4LjczOWMtMS41MjUtNi44NS0xMS45NzktMTMuNTczLTE4Ljg5NC0zMC4zMzggICAgYy02LjAzNy0xNC42MzMtODIuNzk2LTEyNi44NDksMjEuMjg3LTExMC4xODNjMy44MTMtMC43ODktMjcuMTQ2LTk5LjAwMi0xMjQuNTUzLTEwMC41OTljLTk3LjM4NS0xLjU5Ny05NC4xOSwxMTkuNzYyLTk0LjE5LDExOS43NjIiLz4NCjxwYXRoIGQ9Ik0xODguNjA0LDI3NC4zMzRjLTEzLjU3NywxNS4xNjYtOS41ODQsMTcuODI5LTM2LjcyMywyMy40MTdjLTI3LjQ1OSw1LjY2LTExLjMyNiwxNS43MzMtMC43OTcsMTguMzY1YzEyLjc2OCwzLjE5NSw0Mi4zMDcsNy43MTgsNjIuMjY2LTIwLjIyOWM2LjA3OC04LjUwOS0wLjAzNi0yMi4wODYtOC4zODUtMjUuNTQ3Yy00LjAzNC0xLjY3MS05LjQyOC0zLjc2NS0xNi4zNjEsMy45OTR6Ii8+DQo8cGF0aCBkPSJNMTg3LjcxNSwyNzQuMDY5Yy0xLjM2OC04LjkxNywyLjkzLTE5LjUyOCw3LjUzNi0zMS45NDJjNi45MjItMTguNjI2LDIyLjg5My0zNy4yNTUsMTAuMTE3LTk2LjMzOWMtOS41MjMtNDQuMDI5LTczLjM5Ni05LjE2My03My40MzYtMy4xOTNjLTAuMDM5LDUuOTY4LDIuODg5LDMwLjI2LTEuMDY3LDU4LjU0OGMtNS4xNjIsMzYuOTEzLDIzLjQ4OCw2OC4xMzIsNTYuNDc5LDY0LjkzOCIvPg0KPHBhdGggc3R5bGU9ImZpbGw6I0ZGRkZGRjtzdHJva2Utd2lkdGg6NC4xNTU7c3Ryb2tlLWxpbmVjYXA6YnV0dDtzdHJva2UtbGluZWpvaW46bWl0ZXI7IiBkPSJNMTcyLjUxNywxNDEuN2MtMC4yODgsMi4wMzksMy43MzMsNy40OCw4Ljk3Niw4LjIwN2M1LjIzNCwwLjczLDkuNzE0LTMuNTIyLDkuOTk4LTUuNTU5YzAuMjg0LTIuMDM5LTMuNzMyLTQuMjg1LTguOTc3LTUuMDE1Yy01LjIzNy0wLjczMS05LjcxOSwwLjMzMy05Ljk5NiwyLjM2N3oiLz4NCjxwYXRoIHN0eWxlPSJmaWxsOiNGRkZGRkY7c3Ryb2tlLXdpZHRoOjIuMDc3NTtzdHJva2UtbGluZWNhcDpidXR0O3N0cm9rZS1saW5lam9pbjptaXRlcjsiIGQ9Ik0zMzEuOTQxLDEzNy41NDNjMC4yODQsMi4wMzktMy43MzIsNy40OC04Ljk3Niw4LjIwN2MtNS4yMzgsMC43My05LjcxOC0zLjUyMi0xMC4wMDUtNS41NTljLTAuMjc3LTIuMDM5LDMuNzQtNC4yODUsOC45NzktNS4wMTVjNS4yMzktMC43Myw5LjcxOCwwLjMzMywxMC4wMDIsMi4zNjh6Ii8+DQo8cGF0aCBkPSJNMzUwLjY3NiwxMjMuNDMyYzAuODYzLDE1Ljk5NC0zLjQ0NSwyNi44ODgtMy45ODgsNDMuOTE0Yy0wLjgwNCwyNC43NDgsMTEuNzk5LDUzLjA3NC03LjE5MSw4MS40MzUiLz4NCjxwYXRoIHN0eWxlPSJzdHJva2Utd2lkdGg6MzsiIGQ9Ik0wLDYwLjIzMiIvPg0KPC9nPg0KPC9zdmc+');
  }

  section {
    width: 1280px;
    height: 720px;
    font-family: 'Inter', 'Helvetica Neue', Arial, sans-serif;
    font-size: 23px;
    line-height: 1.45;
    color: var(--ink);
    background: var(--paper);
    padding: 74px 90px 96px 90px;
    justify-content: flex-start;
    /* the elephant-blue hairline every slide carries at the top */
    border-top: 6px solid var(--pg);
    box-sizing: border-box;
  }

  /* Percona logo, bottom-right of every slide. ::before carries only the logo, so
     dark masters can invert it to white without touching anything else. The image is
     inlined as a data URI, because relative url() does not resolve in the VS Code preview. */
  section::before {
    content: '';
    position: absolute;
    right: 46px;
    bottom: 30px;
    width: 128px;
    height: 27px;
    background-image: var(--logo);
    background-size: contain;
    background-repeat: no-repeat;
    background-position: right bottom;
    opacity: 0.72;
    z-index: 10;
  }

  section::after { top: 26px; bottom: auto; right: 34px; color: var(--dim); font-size: 15px; }
  section > footer { color: var(--dim); font-size: 15px; }

  h1 { color: var(--pg-deep); font-size: 44px; line-height: 1.12; margin: 0 0 16px; font-weight: 700; letter-spacing: -0.01em; }
  h2 { color: var(--pg-deep); font-size: 34px; line-height: 1.15; margin: 0 0 22px; font-weight: 700; letter-spacing: -0.01em; }
  h3 { color: var(--pg); font-size: 24px; margin: 18px 0 8px; font-weight: 600; }

  strong { color: var(--pg-deep); font-weight: 700; }
  em { color: var(--dim); font-style: normal; }
  a { color: var(--pg); }

  ul, ol { margin: 0; }
  li { margin-bottom: 10px; }
  li::marker { color: var(--pg); }

  code { font-family: 'JetBrains Mono', 'SF Mono', Menlo, monospace; font-size: 0.86em; background: #e9eff4; color: var(--pg-deep); padding: 1px 6px; border-radius: 4px; }
  pre { background: var(--pg-deep); border-radius: 8px; padding: 18px 22px; font-size: 19px; line-height: 1.4; }
  pre code { background: transparent; color: #dce7ef; padding: 0; font-size: 19px; }
  /* Marp ships highlight.js with a light-background palette; on the charcoal-blue
     pre that renders navy-on-navy. Reset every token, then re-tint a few. */
  pre code span { color: #dce7ef; }
  pre code .hljs-attr, pre code .hljs-attribute, pre code .hljs-keyword { color: #8fc7f0; }
  pre code .hljs-string, pre code .hljs-quote { color: #ffd25e; }
  pre code .hljs-number, pre code .hljs-literal, pre code .hljs-type { color: #b7e08a; }
  pre code .hljs-comment { color: #87a3b6; font-style: italic; }
  pre code .hljs-bullet, pre code .hljs-punctuation, pre code .hljs-meta { color: #9fb6c6; }

  /* headings and table headers carry their own ground, so the code pill must not */
  h2 code, h1 code { background: transparent; color: inherit; padding: 0; font-size: 0.95em; }
  section.dark h1 code, section.dark h2 code,
  section.title h1 code, section.title h2 code { background: transparent; color: inherit; padding: 0; }
  th code { background: rgba(255,255,255,0.18); color: #fff; }

  blockquote {
    border-left: 5px solid var(--amber);
    background: #fdf6e7;
    margin: 20px 0 0;
    padding: 12px 20px;
    font-size: 22px;
    color: var(--ink);
  }
  blockquote::before, blockquote::after { content: none; }

  table { font-size: 19px; border-collapse: collapse; margin-top: 6px; }
  th { background: var(--pg); color: #fff; font-weight: 600; text-align: left; }
  th, td { border: 1px solid var(--rule); padding: 8px 12px; }
  tr:nth-child(even) td { background: #eef3f7; }

  /* a label above the heading */
  .kicker { color: var(--pg); font-size: 17px; letter-spacing: 0.1em; text-transform: uppercase; font-weight: 600; margin: 0 0 10px; }

  /* two columns */
  .cols { display: grid; grid-template-columns: 1fr 1fr; gap: 34px; align-items: start; }
  .cols > div > h3:first-child { margin-top: 0; }

  section.dense { font-size: 20px; }
  section.dense li { margin-bottom: 6px; }
  section.dense pre, section.dense pre code { font-size: 17px; }
  section.dense table { font-size: 17px; }

  /* dark beat-change slide */
  section.dark { background: var(--pg-deep); color: #eaf1f6; }
  section.dark h1, section.dark h2 { color: #fff; }
  section.dark strong { color: #ffd25e; }
  section.dark code { background: #2c5370; color: #eaf1f6; }
  section.dark pre code, section.dark pre code span { background: transparent; }
  section.dark { border-top-color: var(--amber); }
  section.dark::before { filter: invert(1) brightness(3); opacity: 0.85; }
  section.dark footer, section.dark header, section.dark::after { color: #8fa9bd; }

  /* title slide */
  section.title { background: var(--pg-deep); color: #fff; justify-content: center; padding-left: 90px; }
  section.title { border-top-color: var(--amber); }
  section.title::before {
    filter: invert(1) brightness(3);
    opacity: 0.95;
    left: 90px;
    right: auto;
    bottom: 58px;
    width: 248px;
    height: 52px;
    background-position: left bottom;
  }
  /* Oversized PostgreSQL mark on the title slide, deliberately cropped by the
     bottom-right corner so it reads as artwork rather than a logo in a row. */
  .pghero {
    position: absolute;
    right: 54px;
    bottom: 52px;
    width: 300px;
    height: 309px;
    background-image: var(--pglogo);
    background-size: contain;
    background-repeat: no-repeat;
    background-position: right bottom;
    filter: brightness(1.45) saturate(1.1);
    opacity: 0.92;
    z-index: 1;
  }

  /* PostgreSQL mark, sits to the left of the Percona mark on the title slide.
     A real element, not ::after, because Marp owns ::after for pagination. */
  .pglogo {
    position: absolute;
    left: 360px;
    bottom: 48px;
    width: 66px;
    height: 68px;
    filter: brightness(1.5) saturate(1.15);
    background-image: var(--pglogo);
    background-size: contain;
    background-repeat: no-repeat;
    background-position: right bottom;
    z-index: 10;
  }
  .closing { display: grid; grid-template-columns: 1fr auto; gap: 56px; align-items: start; margin-top: 6px; }
  .reads { display: grid; grid-template-columns: 1fr 1fr; gap: 34px; margin: 10px 0 0; align-items: stretch; }
  .read { display: flex; flex-direction: column; height: 100%; }
  .me { border-left: 2px solid #3a6180; padding-left: 34px; }
  .me .qr { width: 150px; height: 150px; border-radius: 8px; background: #fff; padding: 5px; box-sizing: border-box; margin-top: 12px; }
  .me .qr img { width: 100%; height: 100%; display: block; }
  .me h3 { margin: 12px 0 3px; font-size: 19px; color: #ffd25e; font-weight: 700; }
  .me p { margin: 0 0 3px; font-size: 15px; color: #dce7ef; line-height: 1.4; }
  .me .u { font-family: 'JetBrains Mono', Menlo, monospace; font-size: 12px; color: #8fa9bd; padding-top: 4px; }
  .read .qr { width: 150px; height: 150px; border-radius: 8px; background: #fff; padding: 5px; box-sizing: border-box; }
  .read .qr img { width: 100%; height: 100%; display: block; }
  .read h3 { margin: 12px 0 3px; font-size: 19px; color: #ffd25e; font-weight: 700; }
  .read p { margin: 0 0 3px; font-size: 15px; color: #dce7ef; line-height: 1.4; }
  .read .u { font-family: 'JetBrains Mono', Menlo, monospace; font-size: 12px; color: #8fa9bd; margin-top: auto; padding-top: 4px; }
  section.title h1 { color: #fff; font-size: 50px; margin-bottom: 10px; }
  section.title h2 { color: #bcd0de; font-size: 27px; font-weight: 400; margin: 0 0 34px; }
  section.title p { font-size: 22px; color: #bcd0de; margin: 6px 0 0; }
  section.title strong { color: #ffd25e; }
  section.title code { background: #2c5370; color: #eaf1f6; }
  section.title .venue { font-size: 19px; color: #8fa9bd; margin-top: 26px; }
  section.title ul { margin: 18px 0 6px; }
  section.title li { color: #dce7ef; margin-bottom: 14px; }

  /* one big statement */
  section.lead { justify-content: center; }
  section.lead h1 { font-size: 46px; }
  section.lead h3 { margin-top: 30px; }

  /* diagrams */
  figure { margin: 10px 0 0; }
  svg { display: block; margin: 0 auto; }
  .d { width: 100%; height: auto; }
  .box { fill: #fff; stroke: var(--pg); stroke-width: 2; }
  .box-a { fill: #fdf6e7; stroke: var(--amber); stroke-width: 2; }
  .box-d { fill: #e9eff4; stroke: var(--dim); stroke-width: 2; }
  .ln { stroke: var(--pg); stroke-width: 2; fill: none; }
  .ln-a { stroke: var(--amber); stroke-width: 2.5; fill: none; stroke-dasharray: 7 5; }
  .t { fill: var(--ink); font-family: 'Inter', Arial, sans-serif; font-size: 16px; }
  .t-s { fill: var(--dim); font-family: 'JetBrains Mono', Menlo, monospace; font-size: 13px; }
  .t-h { fill: var(--pg-deep); font-family: 'Inter', Arial, sans-serif; font-size: 17px; font-weight: 700; }
---

<!-- _class: title -->
<!-- _paginate: false -->
<!-- _header: "" -->

<p class="kicker" style="color:#ffd25e">PostgreSQL on Kubernetes Summit · PGConf.EU 2026, Valencia</p>

# The Anatomy of a PostgreSQL Backup CRD

## pgBackRest, Multi-Repo, and Tiered Storage

**George Kechagias**, Senior Software Engineer, Percona
Percona Operator for PostgreSQL

<p class="venue">2026.pgconf.eu/community-events/pg_on_kubernetes/</p>

<div class="pghero"></div>

<!--
SAY: a backup CR is a thin wrapper around pgBackRest. Read it, and tiered
multi-repo storage is three lines of YAML.

- 10 min. No demo, no live YAML. Arc: engine -> CRD -> storage.
- Say the time budget out loud, so the density reads as deliberate.
-->

---

<!-- _class: lead -->

# `kubectl apply -f backup.yaml`

## What sits between that and a backup in object storage

- The **engine**: pgBackRest, and how it hooks into PostgreSQL
- The **CRD**: what the operator exposes, and what it hides
- The **storage**: multi-repo, and the tiering it unlocks

> One thing drives all three: the **Percona Operator for PostgreSQL**. It writes pgBackRest's config, runs every command it issues, and reconciles the result back into Kubernetes objects. pgBackRest does the work; the operator decides when and with what.

<!--
SAY: the CR is not the backup. It is a request for one.

- Three beats, ~3 min each.
- All of it is the Percona Operator for PostgreSQL (pgv2.percona.com/v2),
  built on Crunchy's PGO.
-->

---

<!-- _class: dense -->

<p class="kicker">The engine</p>

## pgBackRest

### The vocabulary

- **Stanza**: one PostgreSQL cluster's config. The operator always names it `db`
- **Repo**: one backup destination. `repo1`…`repo4`
- **Backup**: `full`, `differential` (since last full), `incremental` (since last backup of any type)
- **WAL archive**: the continuous stream that makes PITR possible

> Everything the operator does is `pgbackrest backup`, `restore`, `archive-push`, `expire`, with the config generated for you.

<!--
SAY: pgBackRest is the whole engine. The operator writes its config and runs it.

- differential = since last FULL. incremental = since last backup of ANY type.
  This is the one people get wrong.
- 60 seconds max here.

IF ASKED "why not pg_dump"
- A dump is one frozen moment, no WAL stream -> no PITR. RPO = time since it ran.
- Logical vs physical: a restore re-runs SQL and rebuilds every index.
- Be fair: pg_dump and pg_restore both parallelise (-j), and a dump can restore
  ONE table where physical is all-or-nothing. "Different jobs."
- What the engine adds: compress-type, repo-cipher (AES-256), process-max,
  checksum-page, --delta, archive-async. Do NOT claim block incrementals:
  needs repo-block=y and the operator does not set it.

IF ASKED about page checksums
- A failure does NOT abort the backup. Docs: "warnings will be emitted... the
  list of invalid pages will be stored in the backup manifest."
- Only does anything when data checksums are on for the cluster.
- Never say "the backup fails immediately".
-->

---

## How pgBackRest attaches to PostgreSQL

<figure>
<svg class="d" viewBox="0 0 1120 335" role="img" aria-label="Three separate data paths: WAL archived by the instance pod itself, backups driven by a Job that execs into the repo host, and restores run locally by a Job">
<path d="M4 112 H 1112" stroke="#d8e1e8" stroke-width="1" fill="none"/>
<path d="M4 222 H 1112" stroke="#d8e1e8" stroke-width="1" fill="none"/>
<text class="t-h" x="6" y="46" style="font-size:26px">1</text>
<text class="t-s" x="6" y="66">WAL</text>
<text class="t-s" x="6" y="83">always on</text>
<rect class="box" x="96" y="14" width="214" height="84" rx="8"/>
<text class="t-h" x="110" y="37">Instance Pod</text>
<text class="t-s" x="110" y="57">container: database</text>
<text class="t-s" x="110" y="75">archive_command =</text>
<text class="t-s" x="110" y="92">pgbackrest archive-push</text>
<path class="ln" d="M310 40 H 788"/>
<polygon fill="#336791" points="800,40 788,34 788,46"/>
<text class="t-s" x="330" y="32">written by the instance itself, repo host not in the path</text>
<rect class="box" x="800" y="20" width="300" height="40" rx="6"/>
<text class="t" x="814" y="45">repo · S3 / GCS / Azure</text>
<path class="ln-a" d="M310 84 H 384"/>
<polygon fill="#cf8a00" points="396,84 384,78 384,90"/>
<rect class="box-d" x="396" y="66" width="164" height="36" rx="6"/>
<text class="t-s" x="410" y="89">Repo Host</text>
<path class="ln-a" d="M560 84 H 788"/>
<polygon fill="#cf8a00" points="800,84 788,78 788,90"/>
<rect x="672" y="72" width="112" height="23" rx="4" fill="#fff" stroke="#cf8a00" stroke-width="1.5"/>
<text class="t-s" x="686" y="88" style="fill:#cf8a00;font-weight:700">PVC only</text>
<rect class="box" x="800" y="66" width="300" height="38" rx="6"/>
<text class="t" x="814" y="90">repo · PVC</text>
<text class="t-h" x="6" y="156" style="font-size:26px">2</text>
<text class="t-s" x="6" y="176">BACKUP</text>
<text class="t-s" x="6" y="193">on demand</text>
<rect class="box-a" x="96" y="124" width="200" height="84" rx="8"/>
<text class="t-h" x="110" y="147">Backup Job</text>
<text class="t-s" x="110" y="172">the repo host runs it</text>
<text class="t-s" x="110" y="192">this Job only logs it</text>
<path class="ln" d="M296 166 H 384"/>
<polygon fill="#336791" points="396,166 384,160 384,172"/>
<text class="t-s" x="300" y="158">pods/exec</text>
<rect class="box" x="396" y="124" width="284" height="84" rx="8"/>
<text class="t-h" x="410" y="147">Repo Host</text>
<text class="t-s" x="410" y="167">pgbackrest backup --repo=N</text>
<text class="t-s" x="410" y="185">reads each instance's data dir</text>
<text class="t-s" x="410" y="202">over mTLS, then writes</text>
<path class="ln" d="M680 166 H 788"/>
<polygon fill="#336791" points="800,166 788,160 788,172"/>
<rect class="box" x="800" y="146" width="300" height="40" rx="6"/>
<text class="t" x="814" y="171">repo · any type</text>
<text class="t-h" x="6" y="268" style="font-size:26px">3</text>
<text class="t-s" x="6" y="288">RESTORE</text>
<text class="t-s" x="6" y="305">in place</text>
<rect class="box-a" x="96" y="236" width="230" height="84" rx="8"/>
<text class="t-h" x="110" y="259">Restore Job</text>
<text class="t-s" x="110" y="279">image: postgres</text>
<text class="t-s" x="110" y="297">mounts the pgdata PVC</text>
<text class="t-s" x="110" y="314">runs pgbackrest restore</text>
<path class="ln-a" d="M326 278 H 788"/>
<polygon fill="#cf8a00" points="800,278 788,272 788,284"/>
<text class="t-s" x="360" y="270">reads the repo itself · no exec, no repo host</text>
<rect class="box" x="800" y="258" width="300" height="40" rx="6"/>
<text class="t" x="814" y="283">repo · any type</text>
</svg>
</figure>

> Three paths, three different actors, one stanza. **WAL** is pushed by the instance itself. A **backup** is a Job that execs into the repo host and relays its output. A **restore** is a Job that reads the repo directly and never touches the repo host.

<!--
SAY: the operator's job is archive_command, plus a TLS mesh between the
instance pods and the repo host.

- archive_command is MANDATORY, not overridable from the CR.
  restore_command = archive-get, used on replica bootstrap and PITR.
- It is not just archive-push: a pg_waldump | grep COMMIT | sed pipeline writes
  /pgdata/latest_commit_timestamp.txt. That is where latestRestorableTime
  comes from.

PVC-ONLY ROUTING IS OUR CHOICE, NOT pgBackRest's. Easy to get backwards.
- Tested 2.59.1: repo1-type=s3 WITH repo1-host is ACCEPTED, failing only on
  transport. Invalid values are rejected outright. S3-behind-a-repo-host is a
  real pgBackRest topology.
- Ours: repoN-host only when repo.Volume != nil.
- The cost: you cannot centralise cloud credentials here. Every instance pod
  holds the bucket keys.

VERIFIED LIVE
- Instance conf with repo1=S3 + repo3=Azure has no repoN-host line.
- The repo host mounts no PersistentVolume: config, certs, emptyDir. It stores
  nothing, and exists even with zero PVC repos.
- It dials OUT to pg1/2/3-host over mTLS, to read PGDATA.
- Port 8432 is pgBackRest's default; we never set tls-server-port.

THE BACKUP JOB IS A REMOTE CONTROL
- /opt/crunchy/bin/pgbackrest is a Go wrapper, not pgBackRest. It pods/execs
  into the repo host and pipes stdout back, hence [pgbackrest:stdout] lines and
  failures as "command terminated with exit code 82".
- So backup logs live ONLY in the Job. The repo host's own log has just
  "server command begin": log-level-file=off.
-->

---

<!-- _class: dark -->

<p class="kicker" style="color:#ffd25e">The CRD</p>

<!-- _class: dense -->

## It all hangs off `repos[]`

```yaml
backups:
  pgbackrest:
    global:
      repo1-retention-full: "2"          # retention lives HERE, not on the repo
    repos:
      - name: repo1                      # ^repo[1-4]$
        volume: { volumeClaimSpec: { resources: { requests: { storage: 100Gi } } } }
        schedules: { full: "0 2 * * 0", incremental: "0 2 * * 1-6" }
      - name: repo2
        s3: { bucket: pg-backups, region: eu-west-1 }
        schedules: { full: "0 4 * * 0", differential: "0 4 * * 3" }
```

- Each repo is **one of** `volume` | `s3` | `gcs` | `azure`, by convention, not by schema
- Schedules are per repo, but **no `CronJob` is ever created**: the operator runs its own cron
- Retention is **not** a CRD field, it is a `global` passthrough to `pgbackrest.conf`

<!--
~3:40 here. This is the beat change, so take the breath HERE.

SAY: the repos array IS the storage architecture. Everything else is detail.

- global is map[string]string straight into pgbackrest.conf. The escape hatch
  for everything the CRD never modelled: compression, process-max, bundling.

NO KUBERNETES CRONJOBS - verified
- kubectl get cronjobs returns "No resources found" with three schedules set.
- ToCrunchy() nils BackupSchedules Full/Differential/Incremental
  (perconapgcluster_types.go:691-696), so Crunchy's CronJob path is dead here.
- Scheduling is an in-process cron (percona/controller/pgcluster/schedule.go)
  that creates a PerconaPGBackup per firing.
- Upside: scheduled and on-demand converge on ONE object and one code path.
  Sharp edge: a firing into a non-ready cluster is dropped with only a log
  line, leaving no Kubernetes object behind.

THE UNION IS DOCUMENTED, NOT ENFORCED
- All four fields are +optional with no CEL rule; the type comment says the
  oneOf KEP is "applicable once implemented".
- Set s3 AND azure and the API accepts it. getExternalRepoConfigs resolves
  azure, else gcs, else s3, and silently ignores the rest.

CUT FOR SPACE: pgBackRest also supports SFTP, which never reached the CRD.
-->

---

<!-- _class: dense -->

## One backup, one object

<div class="cols">
<div>

```yaml
kind: PerconaPGBackup
spec:
  pgCluster: cluster1
  repoName: repo2
  options:
    - --type=full
```

</div>
<div>

```yaml
kind: PerconaPGRestore
spec:
  pgCluster: cluster1
  repoName: repo2
  options:
    - --type=time
    - --target="2026-10-18 14:30:00"
```

</div>
</div>

| `PerconaPGBackup.status` | what it gives you |
| --- | --- |
| `state` | `Starting` → `Running` → `Succeeded` \| `Failed` |
| `backupType` / `storageType` | `full \| differential \| incremental` · `filesystem \| s3 \| gcs \| azure` |
| `backupName` / `destination` | the backup's ID, `20261022-225152F`, and where it went, `s3://…/repo1` |
| `latestRestorableTime` | newest point you could recover to. **now minus this = your RPO** |
| `conditions[BackupLeaseAcquired]` | whether this backup is running or queued behind another |

> `PerconaPGRestore.status` has only `jobName`, `state`, `completed`. Nothing about why.

<!--
SAY: a Backup is a Kubernetes object with a lifecycle, so kubectl get, RBAC,
events and your alerting all work on it for free.

backupName is pgBackRest's own ID, and the chain reads by eye:
    20261022-225152F                     full
    20261022-225152F_20261022-225634D    diff
    20261022-225152F_20261022-225716I    incr
- This is the --set= string. Expire that full and both increments go with it.

latestRestorableTime is THE field to alert on.
- It does not come from the repo: archive_command greps the last COMMIT into
  /pgdata/latest_commit_timestamp.txt, and a watcher execs the primary every 10s.
- If that pipeline stops matching, RPO goes stale while backups stay green.

- Asymmetry, worth a beat: backup status has 16 fields, restore has 3. Backup
  has an Error field; restore does not.

RESTORE IS IN-PLACE, and on a 3-node cluster it is restore ONE, rebuild TWO.
- First every instance runner and Patroni Endpoint is torn down. That Endpoint
  is the leader lock, so the HA layer is dismantled on purpose.
- Exactly one instance is restored: Status.StartupInstance, an FNV hash of
  cluster + instance-set name. Deterministic, NOT whichever pod was primary.
- Its Job (postgres image, mounts pgdata) runs
      bash -xc "pgbackrest restore ${opts}"     # --delta always appended
  inside a script that first sets archive_command='false -- store WAL files
  locally for now' so a half-restored server cannot push into your repo, with
  archive_mode=on and hot_standby=on, and copies max_connections /
  max_locks_per_xact / max_prepared_xacts / max_worker_processes /
  max_wal_senders out of pg_controldata, since hot_standby refuses to start if
  any of them shrank.
- Then PostgresDataInitialized is dropped, the cluster re-bootstraps on the
  restored directory, and that instance becomes primary. Not a wipe-and-rewrite.
- The other two are NOT restored, they are REBUILT from the repo. Patroni's
  create_replica_methods is [pgbackrest, basebackup], so each runs
      pgbackrest restore --delta --stanza=db --repo=N
        --link-map=pg_wal=<wal dir> --type=standby
  (--type=standby because Patroni writes the standby signal itself).
- Only THEN do they stream. Patroni finds the leader through the Kubernetes
  Endpoints DCS (use_endpoints: true) and sets primary_conninfo; the WAL gap is
  closed by restore_command = pgbackrest archive-get until streaming takes over.
  use_slots is false, so the REPO, not the primary, carries a lagging replica.
- RTO consequence worth saying: the repo serves one restore plus two rebuilds,
  concurrently, while the new primary stays comparatively idle. Overridable
  with spec.patroni.createReplicaMethods (K8SPG-704).

- Creating a Backup mutates your cluster: startBackup writes repoName and
  options into spec...pgbackrest.manual plus two annotations. Git diff shows
  drift no human wrote.

IF ASKED "can it do volume snapshots?" - yes, in alpha.
- spec.method enum {pgbackrest, volumeSnapshot}, default pgbackrest; a CEL rule
  makes repoName required only for the pgbackrest method.
- Gate BackupSnapshots, Alpha, default false. Off -> status.Error names the gate.
- Say "a second, alpha method behind a feature gate".
-->

---

## What actually creates the Job

<figure>
<svg class="d" viewBox="0 0 1120 380" role="img" aria-label="Four numbered steps from applying a CR, through the cluster CR, to the Job, with status copied back">
<rect class="box" x="40" y="14" width="330" height="78" rx="8"/>
<text class="t-h" x="92" y="38">PerconaPGBackup</text>
<text class="t-s" x="56" y="57" style="fill:#5b7f9a">pgv2.percona.com/v2</text>
<text class="t-s" x="56" y="78">spec: pgCluster &#183; repoName &#183; options[]</text>
<rect class="box" x="750" y="14" width="330" height="78" rx="8"/>
<text class="t-h" x="802" y="38">PerconaPGRestore</text>
<text class="t-s" x="766" y="57" style="fill:#5b7f9a">pgv2.percona.com/v2</text>
<text class="t-s" x="766" y="78">spec: pgCluster &#183; repoName &#183; options[]</text>
<path class="ln" d="M205 92 V 106"/>
<polygon fill="#336791" points="205,118 199,106 211,106"/>
<text class="t-s" x="221" y="110">pgbackup-controller</text>
<path class="ln" d="M915 92 V 106"/>
<polygon fill="#336791" points="915,118 909,106 921,106"/>
<text class="t-s" x="698" y="110">pgrestore-controller</text>
<rect class="box-a" x="150" y="118" width="820" height="88" rx="8"/>
<text class="t-h" x="168" y="142">PerconaPGCluster</text>
<text class="t-s" x="168" y="160" style="fill:#8a6a24">pgv2.percona.com/v2</text>
<text class="t-s" x="168" y="182">annotation  .../pgbackrest-backup = name</text>
<text class="t-s" x="168" y="198">spec...pgbackrest.manual.repoName</text>
<text class="t-s" x="580" y="182">annotation  .../pgbackrest-restore = name</text>
<text class="t-s" x="580" y="198">spec...restore.enabled = true</text>
<path class="ln" d="M310 206 V 220"/>
<polygon fill="#336791" points="310,232 304,220 316,220"/>
<text class="t-s" x="326" y="224">reconcileManualBackup()</text>
<path class="ln" d="M810 206 V 220"/>
<polygon fill="#336791" points="810,232 804,220 816,220"/>
<text class="t-s" x="604" y="224">reconcileRestoreJob()</text>
<rect class="box" x="150" y="232" width="320" height="52" rx="6"/>
<text class="t" x="168" y="254">Backup Job</text>
<text class="t-s" x="168" y="274" style="fill:#5b7f9a">batch/v1</text>
<rect class="box" x="650" y="232" width="320" height="52" rx="6"/>
<text class="t" x="668" y="254">Restore Job</text>
<text class="t-s" x="668" y="274" style="fill:#5b7f9a">batch/v1</text>
<path class="ln-a" d="M150 258 H 18 V 53 H 28"/>
<polygon fill="#cf8a00" points="40,53 28,47 28,59"/>
<path class="ln-a" d="M970 258 H 1102 V 53 H 1092"/>
<polygon fill="#cf8a00" points="1080,53 1092,47 1092,59"/>
<rect class="box-d" x="150" y="300" width="820" height="56" rx="8"/>
<text class="t-h" x="168" y="321" style="font-size:15px">percona-postgresql-operator &#183; one pod, every controller below</text>
<circle cx="176" cy="341" r="9" fill="#336791"/>
<text x="176" y="345" text-anchor="middle" fill="#fff" style="font:700 11px Inter,Arial,sans-serif">2</text>
<circle cx="196" cy="341" r="9" fill="#cf8a00"/>
<text x="196" y="345" text-anchor="middle" fill="#fff" style="font:700 11px Inter,Arial,sans-serif">4</text>
<text class="t-s" x="212" y="345">pgbackup / pgrestore</text>
<circle cx="452" cy="341" r="9" fill="#336791"/>
<text x="452" y="345" text-anchor="middle" fill="#fff" style="font:700 11px Inter,Arial,sans-serif">3</text>
<text class="t-s" x="468" y="345">cluster reconciler &#183; builds the Job</text>
<circle cx="70" cy="33" r="12" fill="#336791"/>
<text x="70" y="38" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">1</text>
<circle cx="780" cy="33" r="12" fill="#336791"/>
<text x="780" y="38" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">1</text>
<circle cx="181" cy="105" r="12" fill="#336791"/>
<text x="181" y="110" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">2</text>
<circle cx="891" cy="105" r="12" fill="#336791"/>
<text x="891" y="110" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">2</text>
<circle cx="286" cy="219" r="12" fill="#336791"/>
<text x="286" y="224" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">3</text>
<circle cx="834" cy="219" r="12" fill="#336791"/>
<text x="834" y="224" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">3</text>
<rect x="0" y="140" width="58" height="21" rx="4" fill="#fff" stroke="#cf8a00" stroke-width="1.5"/>
<text class="t-s" x="8" y="155" style="fill:#cf8a00;font-weight:700">status</text>
<rect x="1062" y="140" width="58" height="21" rx="4" fill="#fff" stroke="#cf8a00" stroke-width="1.5"/>
<text class="t-s" x="1070" y="155" style="fill:#cf8a00;font-weight:700">status</text>
<circle cx="18" cy="200" r="12" fill="#cf8a00"/>
<text x="18" y="205" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">4</text>
<circle cx="1102" cy="200" r="12" fill="#cf8a00"/>
<text x="1102" y="205" text-anchor="middle" fill="#fff" style="font:700 14px Inter,Arial,sans-serif">4</text>
</svg>
</figure>

> **1** apply a CR &#183; **2** write onto `PerconaPGCluster` &#183; **3** create the Job &#183; **4** copy status back

<!--
SAY: this is why a backup can sit in Starting with nothing visible happening,
and why there are no CronJobs in the namespace.

- startBackup() sets naming.PGBackRestBackup + percona.com/backup-in-progress
  and fills spec.backups.pgbackrest.manual.
- PGBackRestRestore.Start() sets naming.PGBackRestRestore and fills
  spec.backups.pgbackrest.restore with enabled = true.
- Neither controller ever builds a Job. The cluster reconciler does.
- Step 3 is really two hops: PerconaPGCluster -> ToCrunchy() -> PostgresCluster,
  and Crunchy's reconciler only ever watches the latter. A backup that looks
  stuck is usually stuck between them.
- When a restore finishes the controller flips restore.enabled back to false.
  That is the only reason a completed restore does not loop forever.

TWO CONSEQUENCES, say both
- Applying a Backup MUTATES your cluster object: drift no human wrote.
- One backup at a time. backup-in-progress is a mutex; the loser reports
  conditions[BackupLeaseAcquired] = False rather than failing.
-->

---

<!-- _class: dark dense -->

<p class="kicker" style="color:#ffd25e">The storage</p>

# Four repos is not four copies of a backup

<div class="cols">
<div>

**repo1 · S3**

```text
$ pgbackrest info --stanza=db --repo=1
stanza: db
  status: ok
  db (current)
    wal archive min/max (18):
      ...007 / ...00C
    full backup: 20261022-222858F
      database size: 23MB
      repo1: backup size: 3MB
    full backup: 20261022-223113F
      database size: 30.6MB
      repo1: backup size: 3.9MB
```

</div>
<div>

**repo3 · Azure**

```text
$ pgbackrest info --stanza=db --repo=3
stanza: db
  status: error (no valid backups)
  db (current)
    wal archive min/max (18):
      ...005 / ...00C
```

</div>
</div>

> Same cluster, seconds apart. **Identical WAL range** in both repos. Only `repo1` has backups.

<!--
~6:30. This is the slide the talk exists for. Pause on it.

SAY the half that is not on the slide: give repo3 one full backup and every
segment it is already holding becomes restorable.

- Real output. Three-instance PG 18, pgBackRest 2.59.1, repo1=S3, repo3=Azure.
  Only the long WAL ids are abbreviated.
- "status: error (no valid backups)" is pgBackRest's own wording, status code
  2, the same code the operator maps to ErrNoValidBackups.
- Sharp eyes: the ranges START differently and END the same. That is expiry,
  not routing. repo1 has backups so expire trims archive behind them; repo3 has
  none, so nothing of its archive is ever trimmed.

THE AFTER SHOT, same cluster, once repo3 got one full:
    before                            after
    status: error (no valid backups)  status: ok
    wal ...004/...013                 wal ...004/...013
    (no backups)                      full 20261022-225759F, 30.7MB db, 4MB repo
- The WAL range did not move. 4MB on the wire turned ten already-held segments
  into a restorable window.
- repo3 starts at ...004, repo1 at ...005: the repo with NO backups holds MORE
  history. The cold tier quietly has the longest reach.
-->

---

<!-- _class: dense -->

## Tiering four repos

### Each repo buys a different recovery, not another copy

| Repo | Where | Full backups | Keep | Why this rung |
| --- | --- | --- | --- | --- |
| `repo1` | PVC, same AZ | weekly + daily incr | 2 fulls | **fastest restore** |
| `repo2` | S3, same region | weekly + midweek diff | 14 days | the everyday restore |
| `repo3` | another region or cloud | weekly | 30 days | **survives losing a region or provider** |
| `repo4` | S3 infrequent-access | monthly | 7 years | **compliance**, cheapest storage |

- WAL goes to **all four, always**. Only the backup schedule differs, so PITR works from any of them
- Pick at restore time: `repo1` when someone dropped a table, `repo3` when the region is gone
- Cost lever: full backups are expensive, WAL is not. Vary the **full-backup frequency** per repo, never where WAL goes
- All four share **one `Lease` per cluster**, so backups never run in parallel: stagger the schedules

> `archive-push` **always** sends WAL to every repo. The operator turns on `archive-async: "y"` so one unreachable repo cannot stall the others to **one segment ahead** of it.

<!--
SAY: repo1..repo4 is a recovery-time ladder, not four copies of one thing.

ARCHIVE-ASYNC IS OURS (config.go:363). We set archive-async=y and spool-path;
pgBackRest defaults it OFF because it needs that spool path.
- Guide: archive-push always pushes to every repo, and an unreachable repo only
  stops blocking the others when async is on.
- If WAL reaches NO repo, Postgres keeps it in pg_wal and the volume fills.
- TDE exception: pgTDE.walEncryption forces archive-async=n, silently opting
  you out of this slide's promise.

ONE LEASE PER CLUSTER - the thing to add if you add one
- pg-<cluster>-backup-lock. Per cluster, not per repo.
- The loser parks visibly: BackupLeaseAcquired=False, LeaseAlreadyHeld.
- Verified: three backups in the same second, serialised, ALL succeeded. A
  collision costs delay, not loss.

COST, live, one stanza on S3
    full    db 30.5MB   repo 3.9MB
    diff    db 30.6MB   repo 14.5KB   ref: 1 full
    incr    db 30.7MB   repo 16.5KB   ref: 1 full, 1 diff
- Read "backup reference total" out loud: retention counts FULLS, so expiring
  that full takes the chain with it.
- Retention is PER REPO. Verified: repo3 went three fulls to two while repo1
  kept its own two plus an untouched chain.

- archive_timeout=60, so recovery POINT is pinned near a minute on every tier.
  The ladder only changes recovery TIME.
- repo4: do NOT say "Glacier". No storage-class passthrough exists, and
  transitioned objects are unreadable until rehydrated.
-->

---

<!-- _class: dense -->

## Bootstrap a new cluster from an existing repo

```yaml
spec:
  dataSource:
    pgbackrest:
      stanza: db
      repo:
        name: repo1
        s3: { bucket: prod-pg-backups, endpoint: s3.eu-west-1.amazonaws.com, region: eu-west-1 }
      options: ["--type=time", "--target=2026-10-18 09:00:00"]
      configuration: [{ secret: { name: prod-backup-creds } }]
```

- Builds a **brand-new cluster** straight from a repo. The source cluster can be long gone: the repo outlives it
- The credentials only need **read** access to the bucket, so a team can clone prod without being able to touch prod's backups
- The same mechanism builds a **standby cluster** in another region: restore once, then replay WAL from the repo continuously

<!--
SAY: the repo outlives the cluster, so the repo, not the cluster, is the unit
of recovery.

- Lands with platform teams: a nightly "clone prod into staging at 03:00" is
  ~15 lines, and it doubles as your restore test.
-->

---

<!-- _class: dense -->

## Where the abstraction leaks

- **A restore overwrites the cluster you point it at.** Its pods are torn down, `--delta` makes the data directory match the backup and deletes anything that is not in it, then the cluster rebuilds and replays WAL. No dry run, no rollback: for a copy, bootstrap a *new* cluster from the same repo
- **A backup object can vanish, and you never deleted it.** When retention expires a backup in the repo, the operator deletes the matching `PerconaPGBackup`. `kubectl get pg-backup` is a *projection* of `pgbackrest info`, not a record of what you ran
- **Nothing tests your restores.** `Succeeded` means the backup command exited zero, never that the data reads back. Green for a year proves nothing
- **The CRD is narrower and looser than the engine.** `^repo[1-4]$` caps you at four repos where pgBackRest allows 256; `global` is an unvalidated string map, so a typo'd key is ignored in silence

> Backups are a solved problem. Restores are an *operational* problem, and that part is still yours.

<!--
SAY: the three bullets that can cost you data come first, on purpose. The CRD
pedantry is last because it only costs you an afternoon. Be honest here rather
than promotional; this is the slide that earns the Q&A.

BULLET 4, the four-repo cap
- repo257 gives "allowed range is 1 to 256 inclusive", so ^repo[1-4]$ is purely
  the CRD's.
- Inherited from Crunchy, identical pattern. No MaxItems on the array at all.
  Nothing in either codebase documents why four.

BULLET 2, "a backup object can vanish"
- cleanupOutdatedBackups() runs pgbackrest info and deletes any Succeeded
  PerconaPGBackup whose backup is gone from the repo, Job first.
- One-way: the repo is authoritative, the Kubernetes objects are GC'd to match.
- Watched live at retention 2: the oldest full expired and its object vanished.
  The diff and incr survived, because their parent full was not the one
  expired. Retention of 1 would have taken three backups.
- e2e asserts it: 09-check-retention-repo1.yaml.

CUT FOR TIME, bring back if asked
- Repo indexes are positional: removing repo2 orphans the data in it.
- The union is a convention: s3 AND azure is accepted, the operator picks one.
- A restore's status is three fields with no Error at all.

IN RESERVE
- Your RPO metric is a shell pipeline. It can go stale while every backup
  reports Succeeded.
- An expired credential blocks DELETION: delete-backups runs stanza-delete, so
  a rotated key hangs the namespace in Terminating. But the finalizer is fine -
  with working credentials the cascade finishes in under 15s (measured 27s end
  to end). Framing: a credential it cannot use turns a 15-second cleanup into
  an unbounded hang.
-->

---

<!-- _class: lead -->

## Three things to take away

1. The CR asks for a backup. pgBackRest and the repo hold the truth about whether you have one
2. Every repo gets the WAL stream, but a backup lands in **one** repo at a time, and that gap is where your tiering comes from
3. Pick a repo by **how long a restore from it takes**: a local PVC is fastest, another cloud is slowest but survives the most. Know the defaults the operator already chose: `archive-async` on, backups serialised by a lease, and WAL encryption changes both

<!--
~9:30. Stop here even if slides remain.

SAY point 2 again. If they remember one sentence, make it that one.

Live evidence for point 2, repo1=S3 and repo3=Azure, every backup targeting repo1:
    repo1: status ok, wal ...004/...00C, full 20261022-213438F, incr ..._213704I
    repo3: status error (no valid backups), wal ...004/...00C
- Zero backups in repo3, byte-identical WAL range. One archive_command, both
  destinations, every segment, while the backups sit in exactly one of them.
-->

---

<!-- _class: title -->
<!-- _paginate: false -->
<!-- _header: "" -->

# Thank you

<div class="closing">
<div>
<p class="kicker" style="color:#ffd25e;margin:0 0 4px">Interesting reads</p>

<div class="reads">
<div class="read">
<div class="qr"><img alt="QR code to the Crunchy migration guide" src="data:image/png;base64,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"></div>
<h3>Migrating off Crunchy</h3>
<p>Hard fork of Crunchy's. The CRDs coexist during the move.</p>
<p class="u">percona.com/postgresql/migrate-from-crunchy</p>
</div>
<div class="read">
<div class="qr"><img alt="QR code to the operator feature matrix" src="data:image/png;base64,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"></div>
<h3>Feature matrix</h3>
<p>Everything the operator supports, version 3.1.0.</p>
<p class="u">docs.percona.com/.../3.1.0/features.html</p>
</div>
</div>
</div>

<div class="me">
<p class="kicker" style="color:#8fa9bd;margin:0">Find me</p>
<div class="qr"><img alt="QR code to George Kechagias on LinkedIn" src="data:image/png;base64,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"></div>
<h3>George Kechagias</h3>
<p>Senior Software Engineer, Percona</p>
<p class="u">linkedin.com/in/george-kechagias-86118b92</p>
</div>
</div>

<div class="pglogo"></div>

<!--
LIKELY Q&A
- A fifth repo? No. ^repo[1-4]$.
- Does a backup block the primary? It runs on the repo host but reads from the
  primary. backup-standby is not configured at all (verified: the string
  appears nowhere in the repo). An honest gap versus a VM setup; Stefan's talk
  covers backup-standby properly.
- How do I test restores? The dataSource clone flow from "Bootstrap a new
  cluster from an existing repo", on a schedule of your own. Note that YOUR
  CronJob is a real CronJob; only the operator's backup schedules are not.
- Can two repos back up at once? No. One Lease per cluster serialises them, and
  the waiter shows BackupLeaseAcquired=False / LeaseAlreadyHeld. It queues
  rather than failing.
-->
