# Drift Compounds: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Per-step reliability compounds.** An agent that is 95% right at each step of a 12-step workflow
delivers a correct end result about half the time, and the failures arrive *confident and
polished*, not crashed. Checkpoints are a design surface: what a human can still verify, and
where, decides what gets caught.

**The "wait, what?" moment**:

> Set per-step reliability to **95%** (an A grade) and press **Run 100 workflows**. One hundred
> beads flow down the 12-step pipeline; none crashes, all 100 produce a finished, plausible
> output. The tally: **54 correct, 46 silently wrong**. An A-grade step, twelve times in a row,
> is a coin flip.

Two secondary beats, in order:

1. *Buy a better model*: drag to **99%**. Still only **89 of 100**. Even the slider's maximum,
   99.9%, leaks one. The exponent beats the base.
2. *Design the checkpoints*: each checkpoint a student places shows a **coverage bracket over
   the previous three steps**: a human can meaningfully verify recent work, but drift older than
   that has been built upon and reads as foundation, not error. One checkpoint lifts 54 to
   **63**. The student who tries the obvious dodge, one big review at the end, gets the same 63,
   with the bracket showing steps 1–9 uncovered: that is "I review everything" seen honestly.
   Covering the whole rail takes a checkpoint every three steps: **100 of 100 correct**, at the
   cost of 400 reviews and a card that admits the flattery (§5).

## 2. Screen layout

Single column, pipeline dominant:

1. **The pipeline**: 12 named steps in a horizontal rail (wraps to two rows on phones), a
   plausible back-office workflow: *read request → look up account → check contract → draft
   scope → estimate cost → pick vendor → write proposal → apply discount policy → compliance
   check → format document → address cover email → send*. Clickable gaps between steps accept up
   to **4 checkpoints** (a `--user` teal flag: a human decision); each placed checkpoint draws
   its teal coverage bracket over the three steps it can still verify, and uncovered steps are
   visibly bare.
2. **The controls**: per-step reliability slider with discrete stops (**80, 85, 90, 95, 97, 99,
   99.9**, default 95); **Run 100 workflows**; **Reset**.
3. **The run**: beads animate through the rail. A bead that drifts turns from green to amber
   *and keeps going*; at a checkpoint whose bracket covers the step where it went wrong, it
   turns green again (caught and fixed). Beads never stop: every run reaches "send".
4. **The tally**: big paired counts (CORRECT / SILENTLY WRONG), a formula card
   (`0.95¹² ≈ 54 of 100`, live: `p^(uncovered steps)`), a review-cost line
   (`100 reviews per checkpoint`), and the verdict band.

Closing card: *drift compounds because each step trusts the step before. A checkpoint is a human
asking "is this still what we meant?" while the error is still shallow enough to see. A single
review at the end inspects a finished, polished artefact whose early mistakes are now
load-bearing: that is a rubber stamp wearing diligence's clothes.*

## 3. The model and the data

- Each bead × step is a Bernoulli trial at reliability `p`, drawn from seeded mulberry32
  (**seed 29**, one stream, bead-major order). The same 100 trajectories replay identically for
  a given slider stop, so every reachable configuration repeats on the projector.
- A bead is **wrong** from its first failed step onward (drift, not crash); later successful
  steps never heal it.
- **Verification horizon**: a checkpoint placed after step `c` catches (and fixes) a wrong bead
  iff its first uncaught error occurred in steps `c−2 … c`. Older drift passes through unseen.
  Catching within the horizon is perfect (§5 admits this flattery). A fixed bead can drift again
  at later steps.
- Consequence, stated on the formula card: expected correct = `p^u` where `u` = number of steps
  not under any bracket. No checkpoints: u=12. One checkpoint, anywhere fully on the rail: u=9.
  Perfect placement (after 3, 6, 9, 12): u=0. Overlapping brackets waste coverage, and the rail
  shows the waste.

## 4. Derived numbers

Verify at build that seed 29 lands within ±3 of expectation at each spotlight setting; else
reseed and freeze the seed that does:

| Setting | Expectation of 100 | Spotlight |
|---|---|---|
| p=95%, no checkpoints | 0.95¹² ≈ 54 | the moment |
| p=99%, no checkpoints | 0.99¹² ≈ 89 | better model, still leaking |
| p=95%, one checkpoint after 12 | 0.95⁹ ≈ 63 | end review = rubber stamp |
| p=95%, checkpoints after 3, 6, 9, 12 | 100 | coverage, at 400 reviews |

- Review-cost line: `reviews = 100 × checkpoints placed`, against the labelled alternative
  "verify every step of every run: 1,200".
- The verdict band never congratulates: even full coverage names the price ("400 human reviews,
  and a real reviewer is not perfect").

## 5. Deliberate simplifications

- **Perfect catching inside the horizon flatters reality.** Real reviewers miss shallow errors
  too; the card says so wherever 100/100 appears.
- **A hard three-step horizon** is a teaching cartoon of a real gradient (the older the drift,
  the harder to see). The cliff makes coverage legible on the rail.
- **Uniform per-step reliability and independent steps.** Real steps differ and real drift
  correlates (a wrong account number corrupts everything downstream), which makes reality worse
  than the toy, never better.
- **Wrongness is binary** so the tally stays readable.
- **Twelve steps** because the workshop's agent designs run about that long, and 0.95¹² ≈ 0.54
  is the cleanest coin flip in the exponent family.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: seeded trajectories, discrete slider stops, identical tallies every load.
- Reliability is a native `range`; checkpoint gaps are buttons with `aria-pressed` and a keyboard
  path; the tally is `role="status" aria-live="polite"`, announced once per run, not per bead.
- CORRECT / SILENTLY WRONG carried by words; green/amber beads also differ by fill (solid vs
  hatched); coverage brackets pair the teal with the word COVERED.
- Animation respects `prefers-reduced-motion`: beads jump straight to the tally.
- `?present` mode; works from `file://`, at 1024×768, and on a phone.
