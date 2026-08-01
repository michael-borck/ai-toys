# Jevons Dial: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Efficiency is a throttle on price, not on appetite.** When AI work gets cheaper per task,
demand responds; if cheap capability unlocks new uses, total usage, total spend, and total energy
all *rise*. "More efficient, therefore less consumed" has been wrong since 1865.

**The "wait, what?" moment**:

> The firm starts at **$1.00 per task, 100 tasks a day, $100/day**. Drag efficiency until the
> cost falls to **$0.10**: ten times more efficient. The counters land on **~3,980 tasks** and
> **~$398 a day**. The efficiency programme just **quadrupled the bill**, and the energy line
> rose too, even though every single task now uses less power than before.

The secondary beat is the toggle that explains *when* the paradox bites. Flip demand to **"uses
are fixed"** and the same drag makes spend collapse to $10/day: rebound needs *elastic* demand,
new uses unlocked by low prices. The unlock ticker (§3) makes that elasticity concrete: nobody
summarised every meeting at a dollar a task; everybody does at a cent.

## 2. Screen layout

Single column, dial dominant:

1. **The dial**: one large slider, labelled **"Cost per task"**, from **$1.00 down to $0.01**
   (log scale; the interesting decade is at the cheap end). A secondary caption converts it to
   "efficiency: N× better than launch".
2. **The counters**: three stat cards with worded deltas against launch: **Tasks/day**,
   **Spend/day**, **Energy/day (kWh)**. Each carries an up/down arrow *and* the word RISING or
   FALLING; spend is the biggest card because spend is the punchline.
3. **The curve**: a demand-curve chart (price vs tasks/day, log-log), the current operating
   point as a moving dot, the launch point pinned for comparison, and the constant-spend line
   drawn faintly so students can see the dot cross it.
4. **The unlock ticker**: a list of uses that light up as cost falls (§3); lit entries sum to
   the current task count, so the abstraction has line items.
5. **The toggle**: "Cheap AI finds new uses" (default, elastic) vs "Uses are fixed" (inelastic),
   `aria-pressed` buttons. The verdict band names the regime and what it implies for a budget,
   an energy forecast, and a headcount plan.

Closing card: *Jevons watched better steam engines increase Britain's coal burn in 1865. The
question for an AI rollout is never "how much less will we spend per task?"; it is "what will we
start doing once it's this cheap?", and that question is answered by people, not by the model.*

## 3. The model and the data

All computed from closed-form formulas; no PRNG anywhere.

- Demand: constant-elasticity, `tasks(C) = 100 × (C / 1.00)^(−ε)`, with **ε = 1.6** (elastic
  regime) and **ε = 0.3** (fixed-uses regime). At C=$0.10 elastic: 100 × 10^1.6 ≈ 3,981 tasks,
  spend ≈ $398; inelastic: 100 × 10^0.3 ≈ 200 tasks, spend ≈ $20. (The §1 caption for the
  inelastic beat rounds to "$10-ish"; show the computed $20 and let the card say "spend
  collapses".)
- Energy: per-task energy falls with cost, `kWh/task = 0.02 × (C / 1.00)^0.7` (efficiency is
  real, just slower than demand growth). Total = tasks × per-task. Verified at build, C=$0.10
  elastic: per-task drops 5.0× (0.02 → 0.004 kWh), total rises from 2.0 to **15.9 kWh/day**
  (≈8×). Launch point: 100 tasks, $100, 2.0 kWh.
- The unlock ticker, elastic mode only (hand-authored, each with a threshold cost and a task
  weight; weights are calibrated at build so lit weights sum to `tasks(C)` within rounding):

| Unlocks below | Use |
|---|---|
| $1.00 | draft the monthly board report |
| $0.50 | summarise every client meeting |
| $0.25 | first-pass every support ticket |
| $0.10 | draft every outbound email |
| $0.05 | rewrite the knowledge base nightly |
| $0.02 | re-analyse the full ticket archive on every product change |

## 4. Derived numbers

- Spend = `C × tasks(C)`; the sign of `d(spend)/dC` is the whole toy: negative when ε > 1,
  positive when ε < 1. The verdict band states this in words ("every cent cheaper *raises* the
  bill: demand here is elastic").
- The constant-spend line on the chart is `tasks = 100 / C`, labelled "same bill as launch".
- All displayed numbers are rounded to two significant figures with worded multipliers
  ("40× the tasks, 4× the bill"); no long decimals anywhere.

## 5. Deliberate simplifications

- **One elasticity per regime**, constant across the whole price range. Real elasticities vary
  by price and saturate; the two fixed values bracket the honest range.
- **The firm is the world.** Rebound at economy scale involves competition and induced demand
  across firms; one firm keeps the arithmetic on one screen.
- **Energy per task falling as C^0.7** is a plausible coupling, not a measurement.
- **No time axis.** Adoption takes months; the dial teleports between equilibria.
- **Not a forecast.** The toy shows a mechanism, not a prediction about any product's pricing.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: pure formulas, identical on every load.
- The dial is a native `range` (operating on log₁₀(C)); the toggle carries `aria-pressed`; the
  three counters update a single `role="status" aria-live="polite"` summary sentence, announced
  on release rather than on every tick.
- RISING / FALLING carried by words on every counter, not only arrows and colour.
- Verdict colours per STYLE.md; the spend card going up is `--bad` red only in elastic mode,
  because the colour must track the argument, not the arithmetic.
- `?present` mode; works from `file://`, at 1024×768, and on a phone.
