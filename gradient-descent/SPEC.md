# Gradient Descent: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Training is not understanding; it is stepping against a slope.** The "model" is one dial
`w`; the curve is its error at each setting. Training measures the local slope, steps the
other way, repeats. Where it lands is determined by the starting point and the step size
(learning rate), not by any grasp of the right answer: it settles contentedly in a shallow
local valley, and a big enough step throws it off the landscape entirely.

**The "wait, what?" moment**

> The ball starts at w = 8.8. Press Run at the default learning rate: it settles, satisfied,
> in the **shallow valley** (w ≈ 7.38, error ≈ 5.75, 14 steps) and declares itself done.
> Drop the ball at w = 1.0 instead: same curve, same rules, and it settles in the **deep
> valley** (w ≈ 2.35, error ≈ 2.13, 7 steps) — less than half the error. The ball never
> knew the deep valley existed in the first run. It only ever checks the slope under its feet.

## 2. The landscape (authored, no randomness)

Error curve defined by nine control points (w, loss), joined by Catmull-Rom interpolation
(uniform, clamped ends) over w ∈ [0, 10]:

(0, 5.2) (1, 7.4) (2.2, 2.2) (3.6, 4.8) (5, 9.6) (6.4, 6.6) (7.6, 5.8) (9, 7.8) (10, 9.4)

Interpolated minima (the smoothing shifts them slightly off the control points): deep
valley w ≈ 2.35, loss ≈ 2.13; shallow valley w ≈ 7.37, loss ≈ 5.75. Ridge peak between
them w ≈ 5.07, loss ≈ 9.62. Gradient by central difference, ε = 0.001. Step rule:
`w ← w − lr × grad(w) × 0.16`.

Verified behaviours (rebuilds must reproduce; settle = |Δw| < 0.004, cap 200 steps):

| Start w | Learning rate | Outcome |
|---|---|---|
| 8.8 (default drop) | 1.0 | shallow valley, w ≈ 7.38, loss ≈ 5.75, 14 steps |
| 1.0 | 1.0 | deep valley, w ≈ 2.35, loss ≈ 2.13, 7 steps |
| 0.5 | 1.0 | diverged off the left edge in 1 step |
| 2.2 | 2.5 | oscillates around the deep valley, stopped by the 200-step cap |
| 8.8 | 0.05 | shallow valley in 113 steps (same answer, slower) |

## 3. Mechanics

- **Drop the ball**: click anywhere on the plot (w from click position), nudge with
  ←/→ when the chart is focused (0.1, Shift = 0.5), or "Drop ball at start" (w = 8.8).
  Dropping resets the step count and trail.
- **Learning rate**: slider 0.05–2.5, default 1.0.
- **Step / Run**: one gradient step, or auto-step every 420 ms (Pause toggles). Each step
  leaves a trail dot; "Clear trail" removes them.
- **Readouts**: position w, error (curve value at w), steps taken, last step size.
- **Verdicts** (`role="status"`): settled in the deep valley (|w − 2.2| < 0.5) vs the
  shallow one; **DIVERGED** when w leaves [0, 10] (display clamped to [−0.4, 10.4] so the
  ball stays visible at the edge); stopped after 200 steps without settling (the
  too-big-step oscillation case). Once a verdict shows, stepping is inert until the ball
  is dropped again or Run is pressed.
- No randomness, no storage, no network. Every trajectory is arithmetic on the curve above.

## 4. Layout

- Single 1000px column: plot card (SVG 900×380, padding PL=50/PR=18/PT=20/PB=40, loss
  axis 0–11) with readouts, controls card (learning rate, four buttons, verdict band),
  wait-what card.
- All sizes em-based off `--fs` (16px; `?present` → 20px).

## 5. Accessibility floor

- Learning rate is a native range input; all actions are real buttons.
- The chart is focusable (`tabindex="0"`, visible focus ring) with arrow-key ball
  placement, so pointer users and keyboard users get identical power; its aria-label
  states the two-valley setup and the keyboard route in words.
- Position, error, and step count are text beside the chart; the verdict is
  `role="status" aria-live="polite"`. The ball is a bonus visual, never the only signal.
