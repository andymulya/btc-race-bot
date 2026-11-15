# btc-race-bot

[![make.fun · btc-race-24h-20261115-0828](https://make.fun/badge/btc-race-24h-20261115-0828.svg)](https://make.fun/bounty/btc-race-24h-20261115-0828?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261115-0828)
[![rank](https://make.fun/badge/btc-race-24h-20261115-0828/andymulya/btc-race-bot/rank.svg)](https://make.fun/bounty/btc-race-24h-20261115-0828/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261115-0828)
[![reward](https://make.fun/badge/btc-race-24h-20261115-0828/andymulya/btc-race-bot/reward.svg)](https://make.fun/bounty/btc-race-24h-20261115-0828?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261115-0828)

A deterministic LLM-agent BTC trading bot for make.fun's "Race a BTC trading
bot live for 24 hours" bounty ($250 in USDC). It trades cbBTC/USDC on a 1-minute
replay of real on-chain prices (Orca Whirlpool), long only, starting from
$1,000 paper USDC.

Strategy — flush-reversion with gated arms: the bot waits for a real flush
(price at least 100 bps below its 60-bar EMA *and* a 60-bar return of -1.5%
or worse), then scales in as the dip deepens (10% / 30% / 60% / 100% of
equity at 100 / 120 / 140 / 155 bps below EMA60). It rides the recovery with
a trailing exit (armed at +0.5% profit, sells after giving back 0.6% from
the peak), a +4% take-profit, a -6% catastrophic stop, and a 480-bar time
stop. A regime read from the grader's model gateway (one call every 60 bars,
temperature 0) tilts the thresholds and sizes; any gateway failure falls
back to the neutral parameters, so the bot stays deterministic.

Dev-window backtest (official `grade-local`, driver process, 1,440 bars):
net PnL **+$14.64 USDC** (4 trades, win rate 1.0, max drawdown 3.7%),
reproduced byte-for-byte on regrade. Buy-and-hold on the same window loses
about -4%.

## Run it

```sh
node main.ts
```

The bot speaks the bars-replay protocol on stdin/stdout (NDJSON batches from
the harness, one reply per batch). Node 22, standard library only, no
dependencies. To reproduce the dev backtest locally:

```sh
node cli/makefun.mjs grade-local --kit <kit-dir> --bot . --driver process
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=andy-mulya&utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261115-0828) · AI agents build software for cash rewards</sub>
