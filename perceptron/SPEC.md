# Perceptron: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**An AI decision is a weighted sum compared to a threshold — nothing more mystical than that.**
The inputs, the weights, and the threshold are all visible and all editable; the "wait, what?"
is that the verdict flips when the *weights or threshold* change while the *data* stays
identical. The decision lives in the weights and threshold, not in the data.

**The "wait, what?" moment**

> Run with the defaults (7×3 + 6×2 + 8×4 = 65 < 100): REJECTED. Drag the threshold to 60 —
> same data — and the identical inputs now read APPROVED. The data did not change; the
> decision did. Where the decision actually comes from is the point of the toy.

## 2. The scenario

A go/no-go project decision with three scored factors:

| input | value (0–10, default) | weight (0–10, default) |
|---|---|---|
| budget | 7 | 3 |
| team | 6 | 2 |
| market | 8 | 4 |

Threshold: 0–200, step 5, default **100**.

## 3. Mechanics

- total = v₁·w₁ + v₂·w₂ + v₃·w₃. Approve iff total ≥ threshold.
- Six range sliders (values and weights) plus a threshold slider; every change resets the
  display to the ready state (out node grey, `?`, steps hidden, bands cleared).
- Run: data packets animate along the three connections (CSS `animation: flow`, replayed by
  toggling the class with a reflow), the three term lines reveal, then
  `total ≥/< threshold`, then the verdict band (green approve / red reject) and the out node
  recolours. All timing via `await sleep(ms)`.
- Colours follow the house role palette: user-set things teal (`--user`), weights amber
  (`--accent`), the model's node/verdict purple (`--system`); approve `--good`, reject `--bad`.
- No randomness anywhere. The same inputs always produce the same total.

## 4. The scale table (the closer)

| System | Decision nodes | Roughly |
|---|---|---|
| Your perceptron | 1 | one manager's judgement call |
| A small neural network | ~100–1,000 | a department voting together |
| Image recognition | millions | a corporation of specialists |
| Frontier LLMs | hundreds of billions of parameters | weights, not wisdom — tuned, not taught |

(Older drafts said "ChatGPT-4: 175+ billion" — that conflates GPT-3's parameter count with
GPT-4, whose size is undisclosed. Keep the hedged phrasing above.)

## 5. Layout

- Header, then `main{grid-template-columns:minmax(0,1.25fr) minmax(0,1fr)}`: network SVG left,
  controls + calculation + verdict right; the "whole trick" card spans full width below.
- SVG viewBox `0 0 700 340`: inputs at x=120 (y=70/170/270), weight badges at x=258, decision
  node at (400,170) r=52, output at (578,170) r=44.
- All sizes in `em`-scaled units (`--fs` base 16px, `body.present{--fs:20px}`); `?present`
  toggles the class. Grid collapses to one column under 820px.

## 6. Accessibility floor

- Every slider is a native `input[type=range]` with a visible label pair (name + live value);
  keyboard operation is free.
- Verdict is `role="status" aria-live="polite"`; colour is paired with the words
  APPROVED/REJECTED and the arithmetic.
- The SVG carries `role="img"` + an aria-label describing the flow.
- Focus ring: default outline, never removed.
