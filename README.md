# Tiny Bot, Easy $2 — EMA trend rider with a USDC shelter

[![make.fun competition](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1.svg)](https://make.fun/competitions/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![rank](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/samidyt/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1-makefun/rank.svg)](https://make.fun/competitions/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![reward](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/samidyt/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1-makefun/reward.svg)](https://make.fun/competitions/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

A tiny paper-trading bot for the [make.fun](https://make.fun) competition
**u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1** — "Tiny Bot, Easy $2: Beat
Buy-and-Hold on Solana". It trades 7 Solana assets (SOL, WETH, JITOSOL,
RAY, FARTCOIN, POPCAT, WIF) against USDC on hourly bars, with 10,000 USDC
of **paper money: nothing is traded for real**.

## Strategy

A fast cross scalp with a USDC shelter (full reasoning in the header
comment of `main.ts`):

1. **Enter** when EMA(3) crosses above EMA(8) and the 12-hour return is
   better than −1% — no buying a symbol in free fall.
2. **Exit** the whole position as soon as it is worth 1% over its cost —
   take the bounce and leave — or when EMA(3) crosses back below EMA(8),
   on a 1.5% trailing stop, a 1.5% stop-loss, or a single-bar crash over 4%.
3. **Size** at equity / 7 per symbol, capped at the 25% max-weight rule.
   Otherwise sit in USDC — in a falling market the shelter is the edge,
   and short holds keep the bot out of the chop.

## Dev-window result (kit `grade-local`, process driver)

```
net PnL   -52.95 USDC  (return -0.53%, 90% interval -192.72 .. +100.73 USDC)
trading   95 trades over 7 symbols, win rate 0.396, max drawdown 3.05%,
          exposure 0.195, sharpe -0.02, 0 rejected
fees      137.13 USDC; at 2x fees net PnL -190.72 USDC
guardrails met (trades ok, symbols ok, drawdown ok)
L1 regrade reproduced byte for byte
result_sha256 3e1d771617692f809a370dce4b83d881de01d888595836f37b96958fd0f95d1c
```

For comparison on the same dev window (136 hourly bars, buy-and-hold
−$869.34): the best house baseline, macd-cci, scores −$20.22, and
rsi-bollinger −$56.49. The first version of this bot — a plain trend
rider (EMA(5)/EMA(13), hold till cross-down, no take-profit) — scored
−$452.93 on the same window; the +1% take-profit is what closes the gap.

## Run it

```sh
node main.ts   # speaks agent-fun/bars-replay/v1 on stdin/stdout (Node 22+)
```

Standard library only. `vendor/makefun_bars.py` is a byte-identical copy of
the kit's `sdk/makefun_bars.py`.
