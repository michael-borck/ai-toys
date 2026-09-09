# Scenario Sprint: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. What it is

A **team sprint**: 3–5 people, one 60-minute block (45 minutes of team work, 15 minutes of
pitches). Teams take one of four Australian client briefs, frame the ML problem, select and
test a technique, and defend the recommendation in a 3-minute pitch + 1 minute of questions.

**What it teaches:** technique selection is driven by the client's constraint, not by a
default model — and the pitch (the defence) is the deliverable. Each brief makes a different
constraint decisive.

## 2. The four clients and their decisive constraints

| Scenario | Brief owner | Task | Decisive constraint |
|---|---|---|---|
| Melbourne Health Network — readmission prediction (12% base rate) | Chief Medical Officer | classification, explainable for clinicians/regulators | interpretability over accuracy |
| Aussie Fintech — credit risk (8% default) | CTO | classification with fairness constraints | fairness + regulator-facing explanations; "always approve" = 92% headline trap |
| National Supermarket — dynamic pricing | Head of Pricing | regression (price) vs classification (tier) framing decision | the framing choice itself |
| Australian Mining Corp — failure prediction (3% failure rate) | Operations Director | cost-asymmetric classification | error-cost asymmetry: false negatives deadly, false positives expensive |

Each client block: brief quote with attribution, data inventory, 45-minute team task list,
workflow starters (Orange Data Mining widgets, kept because they are the activity's working
substance), pitch checklist, and a debrief angle with cross-links
(`technique-picker`, `threshold-dial`, `k-anonymity`, `k-means-stepper`, `perceptron`).

## 3. Mechanics

- Scenario cards are `<button aria-pressed>` (house selected-state rule): clicking opens that
  client's detail card and closes others; clicking again closes it. Card grid reflows to one
  column on narrow screens.
- Timer: 60:00 countdown, Start/Pause/Reset, `mm:ss` monospace display. Purely a session
  aid; no persistence.
- Pitch guide card: structure (30s/60s/60s/30s), "what good looks like", debrief points with
  forward links to related toys. Debrief framing kept generic.
- No randomness, no storage, no network.

## 4. Layout

- 980px column. Header → timer bar → client-picker grid → four detail cards → pitch guide.
- Detail cards use `.cols` auto-fit grids (brief full-width above; data / tasks / workflows /
  deliverables as four boxes) collapsing on narrow screens.
- All sizes em-based off `--fs` (16px; `?present` → 20px — this page is designed to be
  projected while teams work).

## 5. Accessibility floor

- Scenario picker uses `aria-pressed` selected state (screen readers get it free); detail
  panels are plain toggled blocks in DOM order.
- Native buttons for timer; focus ring untouched.
- All content is text-first: briefs, lists, and checklists (☐ glyph + text), no
  colour-only signals.
