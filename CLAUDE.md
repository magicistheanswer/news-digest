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
