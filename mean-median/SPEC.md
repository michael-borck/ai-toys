# Mean vs Median: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**The mean and the median answer different questions, and a single extreme value can pull the
mean far away from every real person.** The mean shares the total equally (a bill-splitting
number); the median points at the middle person (a description number). A headline claiming
"average pay rose" is meaningless until you know which average moved — and whose money moved it.

**The "wait, what?" moment**

> Drag the owner from $260K to $2M. The mean climbs from $83K to ~$166K — past every actual
> wage in the building — while the median stays pinned at $65K (the 11th person). The counter
> lines make it brutal: "mean moved: +$83K. median moved: $0K."

## 2. Data (frozen, no randomness)

20 staff salaries (in $K): 38, 42, 45, 47, 50, 52, 55, 58, 60, 62, 65, 68, 72, 78, 85, 92,
105, 118, 135, 160 — plus the owner, draggable $40K–$2,000K (default 260).

With the default owner pay: n = 21, median = 11th value = 65, mean = 1747/21 ≈ 83.2.
Verified extremes: owner at 2000 → mean ≈ 166.0, median still 65; owner at 40 → mean ≈ 72.7,
median 62 (a low owner pulls it down too — the mechanism is symmetric).

## 3. Mechanics

- Dot strip on a 0–2M linear axis (SVG 1000×190): sorted salaries as dots alternating between
  two rows; the 11th dot (median) is ink-coloured; the owner dot is amber and **directly
  draggable** (pointer events, snapped to 5K) — the labelled range slider is the same state,
  so keyboard users get identical power.
- Mean line: purple dashed, labelled MEAN. Median line: green solid, labelled MEDIAN.
- Stats cards show live mean/median values plus "moved since you started" deltas
  (baseline = defaults: mean 83K, median 65K).
- The axis is deliberately linear 0–2M: the crowd squishing left while one dot sails right is
  part of the argument, not a rendering flaw.
- No randomness; dragging is user input.

## 4. Layout

- Single 1000px column: header, strip card (chart + stats + slider + how-to note), wait-what
  card (two paragraphs: the observation, then the bill-splitting vs describing distinction and
  the headline question).
- All sizes em-based off `--fs` (16px; `?present` → 20px).

## 5. Accessibility floor

- Slider is native with a live label ("the owner's pay $X"); the SVG carries role="img" +
  aria-label describing the two lines; mean/median values are text beside the chart, so the
  answer never lives in geometry alone.
- Focus ring untouched; the amber dot's drag is a bonus for pointer users — everything is
  reachable from the slider.
