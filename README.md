[![make.fun](https://make.fun/badge/token-radar-01.svg)](https://make.fun/c/token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/xlondze21/token-radar-01/rank.svg)](https://make.fun/c/token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/xlondze21/token-radar-01/reward.svg)](https://make.fun/c/token-radar-01)

# Token Radar — pre-token protocols ranked by implied value

Entry for [Build the best token launch radar](https://make.fun/c/token-radar-01) on make.fun.

The program reads the sealed DefiLlama snapshot (`/input/snapshot.json`),
ranks every pre-token protocol by implied launch value, and hands in a static
dashboard: a sortable, filterable board, a detail view per protocol with its
model breakdown and farm plan, and a separate live-token view for protocols
whose token already trades.

## The model (radar-2)

Implied value follows only from the snapshot's own figures, the same function
for every protocol, no name ever special-cased:

```
implied = round( tvl_usd
  x peer(category)
  x (1 + clamp(change_7d, -60, 150) / 100 x 0.60)
  x (0.70 + min(age_days, 1095) / 1095 x 0.50)
  x (1 + 0.02 x min(chains, 5)) )
```

`peer(category)` is the median market-cap / TVL of the live-token protocols
in the same category (global median when a category has fewer than 3 live
peers), clamped to [0.05, 1.00]. Age is the time since the protocol was first
listed; an unlisted age uses 365 days.

The board covers all 40 watchlist protocols plus every other rankable
pre-token protocol below the $50M floor — wrapped, staked and bridged products,
exchange products and versioned pools are removed, so the board ranks launches
that do not exist yet, not products of tokens that already trade. Each row
carries a 7-step farm plan with costs and times, derived from the protocol's
category, chains, TVL and age.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Node 22 or later, standard library only. The program runs twice and writes
identical bytes: `board.json`, `data.js`, `dashboard/`, `screenshots/` and
`dossier.json` under the output directory.

## Layout

- `main.ts` — entrypoint: reads the snapshot, writes the board and dossier
- `lib/model.ts` — the radar-2 model, board builder and farm plans
- `vendor/makefun-board.ts` — make.fun's dossier library, from the kit
- `dashboard/` — the static page (`index.html`, `app.css`, `app.js`), no network
- `screenshots/` — board, protocol detail and filtered view

## License

MIT. See `LICENSE`.
