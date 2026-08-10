# POGO Trainer Model

**390 real Pokémon GO trainers, plotted — and an honest look at what trainer level can and can't predict.**

This started as a Jupyter notebook fitting a linear regression of trainer level against
three in-game stats. It's now an interactive dashboard built on the same data, plus a
written-up account of where the original model went wrong — which turned out to be the
more interesting result.

Runs entirely in your browser — no backend, no tracking, no external requests.
See [Running it locally](#running-it-locally); a hosted link goes here once it's deployed.

---

## What it shows

| Chapter | What's in it |
|---|---|
| **The cohort** | Who's in the sample, level distribution, and the limitations that shape everything else |
| **The explorer** | Every trainer as a dot. Switch metric, overlay linear / log-linear / median fits, and change outlier handling to watch the fit move |
| **Where do you stand** | Enter your own stats, get your percentile against trainers in your level band |
| **The level-50 wall** | The spread inside the cap, and why it ceilings any level-based model |
| **Model report card** | The original model marked against two alternatives and against reality |
| **Playstyle** | Catches per km vs. battles per catch — level-independent, so it sidesteps the cap entirely |

## The data

A snapshot of one personal friends list, captured **February 2025**, when the level cap
was still 50.

**Handles are anonymised** (`Trainer 001` … `Trainer 390`). Every stat is real and
unmodified — the people behind them didn't sign up to be showcased by name, and the
analysis never needed their handles.

| Column | Notes |
|---|---|
| `level` | 23–50. 50 was the cap at the time |
| `battleswon` | Lifetime cumulative |
| `distancewalked` | Lifetime cumulative. **Unit assumed km** — the capture didn't record the setting |
| `pokemoncaught` | Lifetime cumulative |

At a glance: median level 42 · median 27,184 caught · median 1,800 battles won ·
median 3,422 km walked · **73 trainers (19%) at the cap**.

## What the original model got wrong

The notebook removed outliers with an IQR filter applied in sequence across four columns,
each pass working on the survivors of the last. Because endgame players hold the largest
totals, each pass preferentially removed them:

```
level           390 → 388
battleswon      388 → 333
distancewalked  333 → 303
pokemoncaught   303 → 280      28% of the data dropped
```

**Level-50 trainers went from 73 to 1.** The model was then asked to extrapolate to level
50 — a level it had effectively never seen. Scored against the real median at each level:

| Level | n | Model predicted (caught) | Actual median | Off by |
|---:|---:|---:|---:|---|
| 40 | 39 | 23,276 | 15,604 | ×1.5 high |
| 45 | 10 | 43,697 | 54,877 | ×1.3 low |
| 49 | 7 | 60,034 | 214,438 | ×3.6 low |
| **50** | **73** | **64,118** | **306,606** | **×4.8 low** |

Two more issues worth naming:

- **Growth compounds (≈ ×1.3 per level); the fit was a straight line.** On the full
  dataset a log-linear fit beats it on every metric (caught R² 0.579 vs 0.442). The straight
  line also predicts *zero* Pokémon caught at level 37.6 and negative below — while real
  trainers in the data go down to level 23. The published predictions start at level 35,
  which hides this rather than fixing it.
- **Level 50 is a censored cap, not a value.** 19% of the cohort shares one level, and
  within it the spread is 18× on catches and 80× on battles won. For those trainers level
  carries no information at all — a hard ceiling on this entire approach, and the reason
  the playstyle ratios are the more useful lens.

None of this is hidden in the dashboard — the outlier toggle reproduces the collapse live.

## Repository layout

```
site/                    the dashboard (netlify.toml publishes this as-is)
├── index.html
├── css/style.css
├── js/app.js            stats engine + every chapter
├── data/trainers.json   built artifact — anonymised
└── vendor/              Chart.js + fonts, vendored (zero external requests)
historic/                the original 2025 project, archived
├── stats-model.ipynb    the notebook this all started as
├── trainingdata.csv     the source data, anonymised
└── predictions_level_35_to_50.csv
```

## Running it locally

Static HTML/CSS/JS — no build step, no dependencies.

```bash
cd site && python3 -m http.server 8771
```

Then open <http://127.0.0.1:8771/>. A real HTTP server is needed because the data file is
loaded with `fetch()`, which `file://` blocks.

## Honest limitations

- **A friends list is not a random sample.** It skews toward active, social players. The
  least active trainers are largely invisible here.
- **One snapshot, no per-row timestamps.** Totals are lifetime cumulative, so a five-year
  casual and a one-year grinder are indistinguishable.
- **Distance units are assumed, not recorded.**
- **This describes the pre-rebalance game.** Pokémon GO has since reworked XP and raised
  the cap to 80, so these levels are not comparable to the current game. A second era is
  in progress and will be published when it's complete.

## Part of the Observation Deck

One instrument in the [Observation Deck](https://observation-deck.netlify.app/) — a fleet
of small, live web tools built from a curious mind that loves to learn.

---

Independent fan project, made for the community. Not affiliated with, endorsed by, or
sponsored by Niantic, Scopely, Nintendo, or The Pokémon Company. Pokémon and Pokémon GO
are trademarks of their respective owners.
