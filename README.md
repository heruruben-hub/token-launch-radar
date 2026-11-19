# token-launch-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/heruruben-hub/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/heruruben-hub/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

**Token Radar (radar-2)** ranks pre-token protocols by an implied value computed from the frozen DefiLlama snapshot of 2026-11-14: 758 protocols, 40 on the watchlist, 198 rows on the board. Every protocol with at least $50M of TVL that records no token is covered, plus every other pre-token protocol the watchlist rule would keep, at any TVL. Protocols whose token already trades are set aside with their market caps shown, never mixed into the ranking.

## The model (radar-2)

Implied value is one function of the snapshot's own figures, the same for every row, and no protocol name is special-cased:

```
implied = round(tvl_usd x comparable(category) x growthMult x maturity x chainsMult)
```

- **comparable(category)** — the median market-cap / TVL ratio of the live-token protocols in the same category (at least 5 of them, else the snapshot-wide median), clipped to [0.05, 0.60]. A category whose tokens trade richly against TVL gets a richer multiple.
- **growthMult** — 1 + clamp(change_7d, -50, 100) / 100 x 0.6 when growing, x 0.9 when shrinking: a falling TVL is discounted harder than a rising one is rewarded.
- **maturity** — 0.55 + 0.45 x min(age_days, 1460) / 1460, where age is the time since the protocol was first listed. An unlisted age uses the snapshot's median pre-token age (634 days).
- **chainsMult** — 1 + 0.02 per extra chain, capped at 1.10.

"Records no token" is the honest wording: the snapshot is DefiLlama's record, not proof a token does not exist. The model never predicts whether or when a token is issued.

## The dashboard

A static page with no network: search, filter by category and chain, a watchlist-only toggle, pre-token / live-token tabs, sorting by implied value, TVL, 7-day growth, age, chains or name, a detail view for every protocol with a "Why this rank" breakdown of each factor, an eight-step farm plan with costs and times, and a model sandbox that recomputes the rows live with your own weights (the submitted board keeps the radar-2 defaults). The live view shows each live-token protocol's market-cap / TVL ratio — the comparables the model prices pre-token rows from.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

Then open `out/dashboard/index.html` in a desktop browser. No build step, no network requests — the dashboard is plain static files under `dashboard/`, and the data is in `out/data.js`. Node 22 or later, standard library only.

## Farm plans

Each of the 198 board rows carries eight concrete steps: a fresh wallet, a $10 test deposit and withdrawal first, the category's main action, a second qualifying action, a weekly action, a hold of 30–60 days depending on the protocol's age, claiming only from the protocol's own published link, and a farm log. Every step states what it costs and how long it takes.

---

Entered on [make.fun](https://make.fun/?ref=kh6903y0&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards
