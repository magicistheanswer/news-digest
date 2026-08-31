# news-digest

Static morning news digest, published via GitHub Pages from `main` / `docs`.
See `feeds.yaml` for the source list and mix, and the top-level HTML comment
in `docs/index.html` for the current run's spec/notes.

## Git workflow for the daily scheduled run

Confirmed by the repo owner on 2026-08-31: every scheduled run pushes its
generated `docs/index.html` and `data/history.json` **directly to `main`**.
No PR, no separate long-lived feature branch, no merge step for a human to
approve — that would break the "no user interaction during a scheduled run"
requirement in the routine spec, since nobody is watching to click merge.

If a session is handed a fresh per-session branch (e.g. `claude/...`) by the
harness, treat that as a starting point only: commit the day's digest there
if needed, then push (or fast-forward) that content straight to `main` as
the final step of the run, so GitHub Pages picks it up automatically. Do not
leave a day's digest stranded on a branch other than `main`.

## Article freshness (confirmed by the repo owner on 2026-08-31)

Direct RSS/Atom fetching of the feeds in `feeds.yaml` is blocked by this
sandbox's network egress policy (every feed domain gets rejected by the
proxy). The working fallback is `WebSearch` scoped to each source's domain
(`allowed_domains: ["example.com"]`).

**`WebSearch` ranks by relevance, not recency** - a highly-linked article
from months ago will often outrank yesterday's post. The first run of this
digest shipped several articles that were weeks-to-months old as a result.
Do not repeat that: before adding any article to the digest,

1. Confirm its actual publish date - most of these sites embed it in the
   URL (`/2026/08/27/...`, `-august-27/`); if not, ask explicitly for the
   publish date in a follow-up search rather than trusting the snippet.
2. Only include it if that date is recent relative to the run - roughly
   the last ~7 days for daily-cadence sources (MIT Technology Review, news
   sites), and roughly the last ~2 weeks for weekly/monthly-cadence
   sources (a weekly roundup post, AEP's monthly release notes).
3. If a source in `feeds.yaml` has nothing that clears that bar on a given
   day, skip it for that run and say so in the HTML comment at the top of
   `docs/index.html` (which sources were skipped and why) - do not fill
   the slot with an older article just to hit the 15-article target.
   Freshness beats quota.

## Page layout (confirmed by the repo owner on 2026-08-31)

- **Section order**: render the "Outside — Schweiz / Weltgeschehen / Sport"
  section **above** "Core — Adobe / Martech / AI", not below. This is a
  standing preference, not a one-off - keep it this way on every future run.
- **Mark read**: every article `<article class="card">` carries a
  `data-link="<article url>"` attribute and a `<button class="mark-read">`.
  A small inline `<script>` at the end of `<body>` stores clicked links in
  the visitor's `localStorage` (key `newsDigestReadLinks`) and hides
  matching cards on load; a "Reset read articles" button in the header
  clears it. This is client-side only - per-browser/per-device, no backend,
  nothing sent anywhere, and it does not affect `data/history.json` or what
  future digests include. GitHub Pages is static hosting with no
  server-side state, so this (or something equally client-side, e.g.
  IndexedDB) is the only way to offer a "mark as read" affordance without
  adding a backend - keep this script (or an equivalent) in the template on
  every future run rather than dropping it.
