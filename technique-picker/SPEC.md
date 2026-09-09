# Technique Picker: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Technique selection is driven by the business question and the data — there is no default
model.** Each scenario makes a different constraint decisive: question type, explainability,
data shape, the accuracy/insight trade-off, and sampling bias.

**The "wait, what?" moment**

> Scenario 5. The mining client's dataset looks complete — hourly sensors, two years of clean
> maintenance records — and the correct concern is that the failed machines are *exactly* what
> is missing. You cannot predict failure from data that only contains non-failures. The data
> problem outranks the technique choice.

## 2. Content (five scenarios, each: brief → question → 4 options → verdict)

| # | Client | Right pick | The rule it teaches |
|---|---|---|---|
| 1 | National bank, loan **amounts** | Regression | "how much?" vs "which category?" |
| 2 | Hospital, readmission risk | Decision tree | regulated/high-stakes → interpretability over small accuracy gains |
| 3 | E-commerce review classification, many text features | SVM | wide/sparse/high-dimensional data → SVM |
| 4 | Retail CLV: accuracy *and* drivers wanted | Random forest + feature importance | the both-and compromise |
| 5 | Mining equipment failure, failed machines absent from data | Raise sampling bias | survivorship bias — data before model |

Options deliberately include tempting near-misses ("best accuracy", "most interpretable",
"largest dataset") whose rejection IS the lesson. Feedback has two parts: why the pick is
right/wrong, then a one-line **consultant's rule**.

Course-specific framing scrubbed: "Week 6", the TUSCANE acronym (option letters became plain
concerns: timeliness / volume / sampling bias / coverage), "Orange workshop" CTA. Client-brief
quotes adapted from the companion class activity `scenario-sprint`.

## 3. Mechanics

- One scenario visible at a time (`hidden`), progress bar, scenario counter.
- Options are `<button>`s; correct index per scenario lives in `RIGHT = {1:1, 2:1, 3:1, 4:1, 5:2}`
  (zero-based option index). First click resolves: correct option `.good`, a wrong pick
  `.badpick`, group disabled, feedback revealed, Next enabled.
- Score tracked in memory only (shown once at the end, framed as trivia — the principles card
  is the payload). No storage, no randomness.
- Final card: the five principles, restated as rules, plus cross-links to `perceptron` (the
  mechanics) and `overfit` (why flattering models still betray you).

## 4. Layout

- Single 860px column: header, progress, counter, five scenario cards, final card.
- Client briefs styled as a teal-edged quote block (`--user`); verdict bands good/warn.
- All sizes em-based off `--fs` (16px; `?present` → 20px). Options stack on narrow screens.

## 5. Accessibility floor

- Real `<button>` options (keyboard + focus ring free); feedback blocks are
  `role="status" aria-live="polite"`; correctness is always worded, never colour-only.
- Client quotes attributed in-page so the scenarios read as briefs, not prose.
