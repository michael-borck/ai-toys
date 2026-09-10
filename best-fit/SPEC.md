# Best Fit: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**"Best fit" has a boring, exact definition: the line minimising the total *squared* vertical
error.** Making the squares literal turns the definition into geometry — the flatter, more
"sensible-looking" line through the middle of the cloud carries enormous end squares, and the
least-squares line often looks wrong while being unbeatable.

**The "wait, what?" moment**

> Draw the flat line through the heart of the cloud: the end squares are enormous. Reveal the
> machine's line: it looks off-centre and slightly wrong, yet its total area is the smallest
> any line can reach. Match it with your own handles and the squares visibly balance above and
> below — that balance *is* "best fit."

## 2. Data (frozen, no randomness)

18 (hours, score) points, roughly +4 points/hour with noise:
(1,42) (1.5,45) (2,48) (2.5,44) (3,52) (3.5,55) (4,50) (4.5,58) (5,60) (5.5,57) (6,63)
(6.5,66) (7,62) (7.5,70) (8,68) (8.5,74) (9,72) (9.5,78).

Least-squares optimum computed at runtime by closed form from these points (deterministic):
`m = (n·Σxy − Σx·Σy)/(n·Σx² − (Σx)²)`, `b = (ȳ − m·x̄)`.

## 3. Mechanics

- Your line = two handles A (x≈0.8) and B (x≈9.2), each a focusable `<g role="slider">` with
  pointer drag (pointer capture) and arrow-key adjustment (±0.5, Shift = ±2).
- Each point draws a **literal square**: side = |residual| in screen units, anchored on the
  residual segment (above the line if the point is above, else below). Area ∝ squared error —
  the definition made visible.
- Scoreboard: your total square area, the machine's (closed-form optimum), and your penalty
  % (`youSSE/optSSE − 1`). Within ~0.1% → green "you matched it" band.
- "Reveal the machine's line" draws the dashed green optimum.
- No randomness; frozen data.

## 4. Layout

- Single 1000px column: plot card (SVG 900×430, viewBox with padding PL=55/PR=20/PT=18/PB=42,
  x 0–10 hours, y 30–90 score), scoreboard card (three stat boxes), wait-what card.
- All sizes em-based off `--fs` (16px; `?present` → 20px).

## 5. Accessibility floor

- Handles are `role="slider"` with aria-label (position stated), aria-valuemin/max/now, and
  arrow-key operation; visible focus ring via `:focus-visible`.
- Scoreboard states everything in words; the matched-it verdict is `role="status"
  aria-live="polite"`; squares are a bonus visual, never the only signal.
