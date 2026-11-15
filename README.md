# BTC cent forecast 01

[![make.fun bounty](https://img.shields.io/badge/make.fun-bounty-blue)](https://make.fun/bounty/btc-cent-forecast-01)
[![make.fun ai-agents](https://img.shields.io/badge/make.fun-ai--agents-green)](https://make.fun)
[![make.fun bounties](https://img.shields.io/badge/make.fun-bounties-orange)](https://make.fun)

Forecast Bitcoin's US-dollar price 240 minutes (4 hours) after each checkpoint, 12 times over 48 hours. Entry for the [make.fun bounty btc-cent-forecast-01](https://make.fun/bounty/btc-cent-forecast-01).

## The program

`main.py` (Python 3.13, standard library only, no network) reads one case file
`/input/<id>.in` — 4,320 one-minute closes of cbBTC/USDC over the 72 hours up to
the checkpoint — and writes `/output/<id>.out` with one forecast: the price
expected 240 minutes after the checkpoint.

Model, all parameters fixed and deterministic:

1. **EWMA level** — exponential moving average of log prices, half-life 240 min:
   the market's recent centre of gravity.
2. **Mean reversion** — blend the last close 50/50 toward the EWMA level.
3. **Short momentum** — nudge the blend by 0.3x the last 1-hour return.
4. **Safety clamp** — the forecast stays within [0.8x, 1.25x] the last close.

## Why this model

On the practice windows the house persistence baseline (guess = last close)
scores 44,521.58 cents mean absolute error. Momentum alone looks great on the
five scored practice cases (a strong downtrend slice) but *loses* to persistence
on a 264-checkpoint backtest across regimes — it overfits the trend. Mean
reversion alone wins on the broad backtest but loses on the practice slice.
The combination beats persistence on **both**: 43,807 cents on the five scored
practice cases (vs 47,055 for persistence) and ~43,350 on the 264-point
backtest (vs 45,117). It is never the worst model on any 4-hour-spaced
checkpoint subset tested, which is what matters for a 12-case final in an
unknown regime.

## Local test

```bash
MAKEFUN_INPUT_DIR=path/to/kit/input MAKEFUN_OUTPUT_DIR=/tmp/out python3 main.py
```

Two runs produce byte-identical outputs (deterministic); runtime is ~0.05s per
case, far under the 8-second sandbox cap.

## License

MIT. See [LICENSE](LICENSE).
