# token-launch-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/heruruben-hub/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/heruruben-hub/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

**Token Radar (radar-2)** is a launch radar for DeFi protocols that have not launched a token yet. It reads one frozen DefiLlama snapshot — 2026-11-14, 758 protocols — and ranks the 198 pre-token protocols the watchlist rule would keep: all 40 watchlist slugs (every protocol with at least $50M of TVL that records no token) plus 158 more pre-token protocols below the floor. The 24 live-token protocols with the biggest market caps are shown on their own tab, set aside from the ranking, because their market caps are facts, not a model.

## How to read the board

Each row carries one number, the **implied value**: what the protocol would be worth if it launched a token today, priced the way the market already prices similar tokens. The board is sorted by that number, strongest first.

- **"Records no token"** means DefiLlama's record for the protocol carries no market cap, no CoinGecko id and no symbol. That is what the record says — it is not proof that no token exists, and the dashboard says so wherever the phrase appears.
- **Implied value is a model, not a promise.** It never predicts whether a protocol will issue a token, when it would, or at what price. A high rank means "big, growing, long-running, and in a category the market prices richly" — nothing more.
- **The watchlist is the floor, not the ceiling.** The board is the whole rankable set, not a capped sample: nothing the watchlist rule would keep is left out.
- **Every row can be checked.** Open any protocol to see "Why this rank": TVL, the comparable multiple for its category, seven-day growth, age with no token recorded, and chain count, each with its number — and the page recomputes the row from those same figures and tells you whether it matches board.json.

## The model (radar-2)

One function scores every row, and no protocol name is special-cased:

```
implied = round(tvl_usd x comparable(category) x growthMult x maturity x chainsMult)
```

- **comparable(category)** — the median market-cap / TVL ratio of the live-token protocols in the same category (at least 5 of them, otherwise the snapshot-wide median), clipped to [0.05, 0.60]. A category whose tokens trade richly against TVL gets a richer multiple; a category whose tokens trade cheaply gets a cheaper one.
- **growthMult** — 1 + clamp(change_7d, -50, 100) / 100 × 0.6 when TVL is growing, × 0.9 when it is shrinking. A falling TVL is discounted harder than a rising one is rewarded, because a launch priced off a shrinking base is the riskier claim.
- **maturity** — 0.55 + 0.45 × min(age_days, 1460) / 1460, where age is the time since the protocol was first listed. A protocol with no listed date uses the snapshot's own median pre-token age (634 days), not a guessed number.
- **chainsMult** — 1 + 0.02 per extra chain, capped at 1.10.

The dashboard states the same formula and lets you move the growth and maturity weights in a sandbox that recomputes the board live in the page. The sandbox never changes the submitted board: board.json keeps the radar-2 defaults.

## The dashboard

A static page with no network at all: search by name, category or chain; filter by category and chain; a watchlist-only toggle; pre-token and live-token tabs; sorting by implied value, TVL, seven-day growth, age, chain count or name; a detail view for every protocol with its "Why this rank" breakdown and its farm plan; and a **Copy farm plan** button that copies the plan as plain text for a farm log. The live-token tab shows each live protocol's market-cap / TVL ratio — the comparables the model prices pre-token rows from.

## Farm plans

Each of the 198 board rows carries eight concrete steps, derived from that protocol's own record — its category, its chains, its TVL and its age. The principles are the same for every plan:

1. **Start with a fresh wallet** that holds only the funds you plan to farm, seed phrase kept offline.
2. **Test with $10 first** — deposit and withdraw before the real position, so a bad contract or a wrong network costs a few dollars, not the whole farm.
3. **Then take the main position** for the protocol's category (supply, provide liquidity, stake, deposit into a vault, and so on), sized to the protocol's TVL: $50–$250.
4. **Do a second, different qualifying action** each week, and one qualifying action every week after that.
5. **Hold 30–60 days** depending on the protocol's age, so the activity is counted in more than one snapshot.
6. **Claim only from the protocol's own published link**, and never pay to claim.
7. **Write it all in a farm log** — wallet, dates, every transaction — so the position can be repeated or unwound.

Every step states what it costs and how long it takes. Nothing here is financial advice, and no step promises an airdrop.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

Then open `out/dashboard/index.html` in a desktop browser. No build step, no network requests — the dashboard is plain static files under `dashboard/`, and the data is in `out/data.js`. Node 22 or later, standard library only. The program is deterministic: run it twice into two directories and `diff -r` the outputs — they are byte-identical.

```sh
mkdir -p out2 && MAKEFUN_OUTPUT_DIR=out2 node main.ts --input <path to snapshot.json>
diff -r out out2 && echo "deterministic"
```

---

Entered on [make.fun](https://make.fun/?ref=kh6903y0&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards
