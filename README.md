# LMA Trade Artifacts — AI for the Trades Deck Library

> Mirrored into this repo so the library docs are viewable on GitHub. The library workspace itself (deck sources, PUBLISHING.md, restyle recipe) lives on the local machine.

A library of **12 self-contained pitch and curriculum decks** advocating AI core skills for
blue-collar and trade-diploma education, plus a **launch pad** that links them all. Every deck
is a single HTML page hosted free on GitHub Pages — no servers, no CI, no framework lock-in for
visitors.

**Launch pad:** https://toddwmac.github.io/trade-launch/

## How the system works

```
lma-trade-artifacts\                     ← this folder (intentionally NOT a git repo)
├── README.md                            ← you are here
├── PUBLISHING.md                        ← step-by-step publish & update procedures (the ops manual)
├── trade-launch\                        ← the launch pad (its own GitHub repo, static HTML, no build)
└── restyled\                            ← the 12 deck projects + restyle tooling
    ├── RESTYLE-ACADEMIC.md              ← the design recipe (dark "shop-floor" → light "academic journal")
    ├── _restyle_batch*.py, _restyle_deck_fix.py   ← historical one-time batch scripts (already applied)
    └── <deck project>\                  ← React 19 + Vite 7 + Tailwind v4 source
        ├── public\.nojekyll             ← copied into dist on every build (keeps Pages raw)
        └── dist\                        ← THE BUILD = THE WEBSITE = A GIT REPO
            └── .git                     ← pushing this folder updates the live site
```

**The core idea (the "dist-repo" pattern):** each deck builds via `vite-plugin-singlefile` into
one self-contained `dist\index.html`. That `dist` folder *is* a git repository, pushed to a
public GitHub repo with Pages enabled. Updating a deck = rebuild + commit + push from `dist`.
GitHub re-publishes automatically in under a minute.

**Live sites** — slug convention: `{model}-{topic}-{type}`, type is `pitch` or `skills`.
Full mapping table lives in **PUBLISHING.md** (local workspace).

| Deck | URL |
|---|---|
| Launch pad | https://toddwmac.github.io/trade-launch/ |
| inking-hvac-pitch | https://toddwmac.github.io/inking-hvac-pitch/ |
| gemini-hvac-pitch | https://toddwmac.github.io/gemini-hvac-pitch/ |
| gpt5-hvac-pitch (vanilla JS) | https://toddwmac.github.io/gpt5-hvac-pitch/ |
| opus5max-hvac-skills | https://toddwmac.github.io/opus5max-hvac-skills/ |
| muse-electrical-pitch | https://toddwmac.github.io/muse-electrical-pitch/ |
| opus-electrical-pitch | https://toddwmac.github.io/opus-electrical-pitch/ |
| gpt56-electrical-skills | https://toddwmac.github.io/gpt56-electrical-skills/ |
| inking-electrical-skills | https://toddwmac.github.io/inking-electrical-skills/ |
| mysteryb-electrical-skills | https://toddwmac.github.io/mysteryb-electrical-skills/ |
| glm52-electrical-skills | https://toddwmac.github.io/glm52-electrical-skills/ |
| qwen38-trades-skills (all trades) | https://toddwmac.github.io/qwen38-trades-skills/ |
| mystery-pilot (position paper) | https://toddwmac.github.io/mystery-pilot/ |

## Updating an existing deck

Content and style edits happen in the project source (`restyled\<deck>\src\`); publishing is
always the same three-command cycle:

1. **Iterate:** `npm install` (first time only — `node_modules` are kept out of the repos),
   then `npm run dev` for the live-reload dev server and edit `src\` as needed.
2. **Build:** `npm run build` — this wipes `dist` (except `.git`, which Vite preserves) and
   writes a fresh self-contained `index.html` plus the `.nojekyll` re-emitted from `public\`.
3. **Publish:** from inside `dist\`: `git add -A`, `git commit -m "..."`, `git push`.
   Live in ~1 minute.

To change a deck's **launch-pad card** (title, blurb, grouping), edit `trade-launch\index.html`
and commit + push from `trade-launch\` — the pad is plain static HTML with no build step.

The visual design language is documented in [RESTYLE-ACADEMIC.md](restyled/RESTYLE-ACADEMIC.md)
(paper `#faf8f4`, oxford blue `#1e4e8c`, Source Serif 4 display type). The `_restyle_*.py`
scripts are historical — the batch restyle they performed has already been applied to all decks.

## Adding a new deck

Two paths, depending on how heavyweight the deck is.

### Path A — new repo (recommended for real decks)

1. Put the project in a new folder (e.g. `restyled\<new-deck>\`). For a generated export, that
   usually means unzipping and reading its `package.json` (per the recipe in
   RESTYLE-ACADEMIC.md). For best results it should build to a **single self-contained HTML
   file** (Vite + `vite-plugin-singlefile`).
2. Create `public\.nojekyll` (empty) so every build re-emits the Pages flag.
3. `npm install && npm run build`.
4. Turn `dist\` into a repo and publish (full commands in
   **PUBLISHING.md** (local workspace)):
   `git init -b main` → commit → `gh repo create <name> --public --source . --push` →
   enable Pages via the `gh api` call shown there.
5. Name it `{model}-{topic}-{type}` in lowercase hyphens (see live slugs above).
6. Add a card to `trade-launch\index.html` linking the new
   `https://toddwmac.github.io/<name>/` URL; commit + push `trade-launch\`.

### Path B — drop it into the launch repo (lightweight)

For simple one-off decks that don't need their own repo or version history:

1. Copy the deck's self-contained `index.html` into `trade-launch\<slug>\index.html`
   (create the subfolder).
2. Commit + push `trade-launch\` — it goes live at
   `https://toddwmac.github.io/trade-launch/<slug>/` automatically.
3. Add a card to `trade-launch\index.html` pointing at that subpath URL.

Note the trade-off: Path B versions the deck together with the launch pad, and its URL is
nested under `trade-launch`; Path A keeps decks independent and portable.

## Rules & gotchas

- **Never delete a deck's `dist` folder wholesale** — it contains that deck's git history.
  Rebuilding is safe; deleting is not.
- `.nojekyll` always comes from the project's `public\` folder — never hand-add it to `dist`.
- Deck repos must be **public** (free-plan GitHub Pages requirement).
- One GitHub repo per deck — the `dist` repo *is* the published site.
- `node_modules` are intentionally absent; run `npm install` in a project before its first build.
- The parent folder is deliberately **not** a git repo (the 13 repos embedded inside it would
  become untracked gitlinks). Don't `git init` here without a plan for the embedded repos.
- These decks are subpath-safe by construction (no absolute asset paths), so they work at any
  URL depth — that's why the same file serves from any Pages project site.

## Key documents

| File | Purpose |
|---|---|
| **PUBLISHING.md** (local workspace) | The ops manual: exact publish/update commands, gotchas |
| **restyled/RESTYLE-ACADEMIC.md** (local workspace) | The design recipe used to restyle every deck |
| `restyled/_restyle_batch*.py` | Historical one-time batch restyle scripts (already applied) |
