# AI Toys

Small self-contained web pages, each built to break **one** specific AI/ML misconception that
lecture slides reliably fail to break. Companion collection to
[security-toys](https://github.com/michael-borck/security-toys), same house rules, same identity.

**▶ [michael-borck.github.io/ai-toys](https://michael-borck.github.io/ai-toys/)**

No install, no account, no network, no backend, no real model. Save any page and it still works:
in an offline lab, from a USB stick, or uploaded to an LMS as a single file. Any toy that
"summarises" or "predicts" is a scripted, seeded simulation and says so on-page.

## The toys

| Toy | Kills | Status |
|---|---|---|
| **[Threshold Dial](threshold-dial/)** | "overall accuracy means it works": the prettiest headline ships 29 of 50 faults, and a detector that detects nothing scores 95% | ✅ ready |
| **[Data or Command?](data-or-command/)** | "a document is just data": drag one hidden sentence into a document and watch the summary flip | ✅ ready |
| **[K-Anonymity](k-anonymity/)** | "there are no names, so it's anonymous": the nameless table opens at k=1 and one row is uniquely the CFO | ✅ ready |
| **[Next Token](next-token/)** | "the model knows things": same prompt, two runs, two answers; the model weighs, it does not know | ✅ ready |
| **[Overfit](overfit/)** | "higher training accuracy means a better model": train climbs to 100% while test falls to 73% | ✅ ready |
| **[Drift Compounds](drift-compounds/)** | "95% reliable per step is basically reliable": over 12 steps it is a coin flip, and the failures arrive polished | ✅ ready |
| **[Jevons Dial](jevons-dial/)** | "more efficient means less used": halve the unit cost and watch total spend rise | 📋 planned |
| **[K-Means Stepper](k-means-stepper/)** | "the clusters are in the data": same data, two starts, two confident truths | 📋 planned |

Add `?present` to any toy (e.g. `threshold-dial/?present`) to bump type sizes for a projector.

## Course mapping

This repo is **course-agnostic and numbering-agnostic**; folders are named for what the toy *is*.
The mapping lives here rather than in the folder names. Adopting these in a unit? Add a column.

| Toy | Curtin ISYS6014 module |
|---|:--:|
| `next-token` | 01 What changed? |
| `data-or-command` | 03 Can you tell? |
| `overfit` | 04 What's in the data? · 11 How does it decide? |
| `k-anonymity` | 06 Whose data is it? |
| `k-means-stepper` | 08 What is it saying? |
| `threshold-dial` | 09 Does it actually work? |
| `drift-compounds` | 10 Can it do the whole job? |
| `jevons-dial` | 12 What comes next? |

## House rules

Every toy obeys all seven. They're what make eight pages feel like one set:

1. **Single self-contained `index.html`.** No CDN, no external fonts, no `fetch`. This is the
   load-bearing rule: it survives an offline lab, an LMS upload, and 2029.
2. **One idea per toy.** If it needs a tutorial, it's too big.
3. **Playable in 90 seconds** from cold, by someone who read no instructions.
4. **A named "wait, what?" moment**: the instant the misconception breaks. It's in every `SPEC.md`.
   If a build doesn't produce it, the build isn't finished.
5. **Shared identity**: the palette and type scale in [`STYLE.md`](STYLE.md), shared with
   security-toys so the two collections read as one family.
6. **`?present` mode** for projection.
7. **A `SPEC.md` per toy**, sufficient to rebuild it from scratch.

Plus two absolutes:

- **Nothing leaves the page.** No storage, no analytics, no backend, anywhere in this collection.
- **Seeded determinism.** Random-looking data comes from a seeded PRNG (mulberry32), so a class can
  argue about the same numbers and a demo repeats. `Math.random()` here is a bug.

## Offline bundle

```bash
./package.sh          # → ai-toys-offline.zip
```

Unzip, open `index.html`, no server. That's the artefact for a lab machine with no internet, an LMS
Content Collection upload, or a student who wants to keep them.

## Adding a toy

1. `cp -r threshold-dial/ new-toy/`; the scaffold *is* an existing toy
2. Write `index.html`; paste the tokens from [`STYLE.md`](STYLE.md)
3. Write `SPEC.md` and a short `README.md`
4. Add a card to the root `index.html` and a row to the tables above

## What this deliberately doesn't have

No test framework (the check is manual: opens from `file://`, works with no network, readable at
1024×768 on a projector, usable on a phone, keyboard-navigable). No analytics. No framework, no
TypeScript, no build step. No backend. No API keys, no model downloads: every "model" is a
deterministic simulation.

## Licence

MIT. Unit-agnostic teaching material; no institution or course branding.
