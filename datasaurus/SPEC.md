# Datasaurus: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Summary statistics do not determine the shape of the data.** Thirteen datasets share means,
standard deviations, and correlation to two decimal places while looking like completely
different things — a dinosaur, a star, concentric rings, grid lines. Any analysis that only
keeps the summary (a report table, a dashboard tile, headline metrics) cannot tell a tidy
trend from a dinosaur.

**The "wait, what?" moment**

> Switch from Dinosaur to Star: the picture changes completely and the stats panel moves
> not one pixel (means 54.26/47.83, SDs 16.71/26.84, r ≈ −0.06, n = 142 — computed live, so
> the refusal to move is honest). "Reveal all thirteen" is the payoff: the wall of shapes
> under one frozen stat line.

## 2. Data

The published **Datasaurus Dozen** (Matejka & Fitzmaurice, Autodesk Research — "Same Stats,
Different Graphs"), after Alberto Cairo's original Datasaurus; same lineage as Anscombe's
quartet (Wikipedia, 1973). 13 datasets × 142 (x, y) points, embedded verbatim (2-dp) as
`const SETS={name:[x,y,x,y,…]}` (~22 KB). Frozen, so determinism holds. Attribution is
rendered on-page (house rule: no network).

Dataset keys → labels: dino→Dinosaur, away→Away, h_lines→Horizontal lines, v_lines→Vertical
lines, x_shape→X shape, star→Star, high_lines→High lines, dots→Dots, circle→Circle,
bullseye→Bullseye, slant_up→Slant up, slant_down→Slant down, wide_lines→Wide lines.

## 3. Mechanics

- Global x/y bounds computed once across all datasets so every plot shares axes (stable axes
  make the shape change, not the frame change).
- Main scatter: SVG 400×300 viewBox, points drawn as circles (r 3.2) by `drawSet()`.
- Stats panel: mean x/y, SD x/y, correlation, n — **computed live from the plotted points**
  (sample SD), not hardcoded, so the identical values are demonstrated, not asserted.
- Picker: one button per dataset; active button amber.
- "Reveal all thirteen": 13 small-multiple SVGs (r 2.6) with captions in a responsive wall;
  toggles.
- No randomness, no storage, no network.

## 4. Layout

Two-column main (scatter left, stats + reveal + debrief right; stacks under 820px), then the
full-width debrief card. All sizes em-based off `--fs` (16px; `?present` → 20px).

## 5. Accessibility floor

- Scatter carries `role="img"` + aria-label stating the invariant ("the summary statistics
  stay the same for every dataset").
- Picker buttons are real buttons with visible labels; focus ring untouched.
- The lesson is stated in words (debrief card), not carried by the shapes alone.
