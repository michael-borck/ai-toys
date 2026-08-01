# Threshold Dial: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Overall accuracy is not evidence the model works.** The headline number can be maximised by a
detector that never detects, and the threshold is a business decision about which error hurts more.

**The "wait, what?" moment** (if a build doesn't produce it, it isn't finished; numbers below
are verified against the frozen data in §3):

> Press **Highest accuracy**. The dial lands on threshold 64: headline **96.5%**, the best
> scorecard the model can produce. The confusion matrix shows what it bought: **29 of the 50
> faulty units ship**. And the fine print under the headline delivers the kill: a detector that
> flags *nothing at all* scores **95.0%**, one and a half points behind your best effort.
> Accuracy cannot tell your detector from a dead one.

The secondary beat: switch on **"slice by line"** at that same accuracy-optimal threshold: Line A
recall **60%**, Line B recall **15%**, a 4× gap the headline never showed, because the model
scores Line B faults lower. Then press **Cheapest**: at the cost-optimal threshold 42 the gap
nearly closes (**97% vs 90%**). Pricing the errors honestly caught the faults *and* treated the
two lines almost equally; chasing the headline did neither. Same arithmetic, wearing a fairness
costume.

## 2. Screen layout

Two columns, collapsing below 860px:

1. **The scores**: a 101-bar histogram of fault scores for 1,000 units off the line, faulty units
   marked as red pins, a shaded "flagged" region, the threshold slider, three preset buttons
   (*Highest accuracy* / *Catch every fault* / *Cheapest*).
2. **The confusion matrix**: a 2×2 card (flagged-faulty, flagged-good, missed-faulty, passed-good)
   with counts, each cell labelled in words.
3. **The scorecard**: headline accuracy, precision ("1 in N flags is a real fault"), recall
   ("N of 50 faults caught"), and a per-line recall row that appears when *slice by line* is on.
4. **What it costs**: two editable cost inputs (missed fault, default **$400**; false alarm,
   default **$5**), a live total-cost figure, and a verdict band that always names what the current
   threshold gives up.

A full-width closing card states the lesson: *accuracy is a summary, the matrix is the truth, and
the right threshold comes from the cost of each error, not from the prettiest headline.*

Bar heights use a square-root scale, as in detection-dial; linear would hide the faulty tail.

## 3. The data

1,000 units per day; **50 faulty** (5% base rate), 950 good. Scores 0–100.

Good units: clamped normal **μ=30, σ=14**, seeded mulberry32, **seed 41**. Identical on every load.

Faulty units are split across two production lines, and the split is the lesson:

| Group | Count | Score distribution | Why |
|---|---|---|---|
| Line A faults | 30 | fixed set, drawn once from μ=68, σ=10, then frozen as constants | well represented in training |
| Line B faults | 20 | fixed set, drawn once from μ=48, σ=12, then frozen as constants | under-represented; scores sit in the good-unit overlap |

Fault scores are generated once (seeds 42 and 43, same Box-Muller pipeline) and **frozen as
constants in the source**, like detection-dial's intrusions. The frozen values:

```
Line A: 35,50,50,54,56,57,58,58,59,61,62,63,64,65,65,65,65,70,71,71,72,72,73,74,78,78,79,80,83,92
Line B: 31,38,42,45,45,46,47,48,50,50,52,52,57,60,60,60,61,67,69,76
```

Verified teaching numbers from this data (rebuilds must reproduce them):

| Threshold | Headline accuracy | Faults caught | Line A / Line B recall | Total cost ($400/$5) |
|---|---|---|---|---|
| 64 (Highest accuracy) | **96.5%** (the argmax) | 21 of 50 | 60% / **15%** | $11,630 |
| 100 (flag nothing) | 95.0% | 0 of 50 | 0% / 0% | $20,000 |
| 42 (Cheapest) | 81.6% | 47 of 50 | 97% / 90% | **$2,105** |
| 31 (Catch every fault) | 56.4% | 50 of 50 | 100% / 100% | $2,180 |

## 4. Derived numbers

- `flagged = good_at_or_above(T) + faults_at_or_above(T)`
- `accuracy = (TP + TN) / 1000`, shown big, because the toy's job is to discredit it
- `precision` shown as "1 in N", never a percentage
- `recall` shown as "N of 50", per line when sliced
- `total cost = FN × missed-fault cost + FP × false-alarm cost`
- The **Cheapest** preset finds the threshold minimising total cost under the current inputs;
  computed, not hard-coded, so editing a cost moves it live. Watching the preset jump when you
  change $400 to $4,000 *is* "the threshold is a business decision".
- **Highest accuracy** preset: argmax of accuracy (lands at or near "flag nothing"). **Catch every
  fault** preset: lowest T with recall 50/50; the verdict band prices the resulting false alarms.

The verdict band never congratulates. Even the cost-optimal setting names the faults it ships.

## 5. Deliberate simplifications

- **One score per unit.** Real QC stacks correlated checks; that escape route is the closing
  card's point, not the toy's.
- **5% base rate is generous.** Real fault rates are lower, which makes precision worse.
- **Two lines only.** Real subgroup structure is messier; two is enough to break the headline.
- **Costs are illustrative.** Change them and the shape holds; only the preset moves.
- **Not a benchmark.** Nothing here describes any real model or vendor.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: same numbers every load (seeded PRNG + frozen fault scores).
- `?present` adds `.present` to `<body>`, raising `--fs` 16px → 20px; everything sized in `em`.
- Threshold is a native `range` input; verdict is `role="status" aria-live="polite"`; presets and
  the slice toggle carry `aria-pressed`.
- CAUGHT / MISSED / SHIPPED carried by words, not only colour; verdict colours per STYLE.md.
- Works from `file://`, at 1024×768 on a projector, and on a phone.
