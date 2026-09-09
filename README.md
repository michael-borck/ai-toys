# AI Toys

Small self-contained web pages, each built to break **one** specific AI/ML misconception that
lecture slides reliably fail to break. Companion collection to
[security-toys](https://github.com/michael-borck/security-toys) and
[programming-toys](https://github.com/michael-borck/programming-toys), same house rules, same identity.

**▶ [aitoys.borck.education](https://aitoys.borck.education/)**

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
| **[Jevons Dial](jevons-dial/)** | "more efficient means less used": make it 10× cheaper per task and the bill quadruples | ✅ ready |
| **[K-Means Stepper](k-means-stepper/)** | "the clusters are in the data": same data, two starts, two confident truths | ✅ ready |
| **[Perceptron](perceptron/)** | "AI decisions are mysterious": three weights and a threshold — change a weight, flip the verdict, same data | ✅ ready |
| **[Pattern Spotter](pattern-spotter/)** | "the data speaks for itself": three charts, and more than one confident story fits each | ✅ ready |
| **[Technique Picker](technique-picker/)** | "there is a default AI technique": five clients, five briefs, five different right answers | ✅ ready |
| **[Scenario Sprint](scenario-sprint/)** | "technique selection is a technical detail": four client briefs, one 60-minute team sprint — frame it, pick it, defend it | ✅ ready |

Add `?present` to any toy (e.g. `threshold-dial/?present`) to bump type sizes for a projector.

**Companion:** [The Trust Tool](https://trusttool.borck.education/)
([repo](https://github.com/michael-borck/trust-tool)) — a drag-the-scenario 2×2 game about where
to trust AI output. Same audience, but deliberately not a toy (multi-file, 16 scenario packs,
    remembers best scores), so it lives in its own repo and gets a card on the landing page instead.

## House rules

Every toy obeys all seven. They're what make twelve pages feel like one set:

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

## Related

Catalogued under Resources on [teach.borck.education](https://teach.borck.education).
Sibling collections: [security-toys](https://securitytoys.borck.education), which set the pattern,
and [programming-toys](https://programmingtoys.borck.education), which applies it to programming
misconceptions.

## Licence

MIT. Unit-agnostic teaching material; no institution or course branding.
