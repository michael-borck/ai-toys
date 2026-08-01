# Next Token: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**A language model emits a probability distribution over next tokens and samples from it.**
It weighs; it does not know. "Hallucination" is not a malfunction of that process, it *is* that
process, running on a prompt whose true answer was never in the weights.

**The "wait, what?" moment**:

> Prompt: "The capital of Australia is". Press **Sample**: "Canberra". Press **Run again**:
> **"Sydney"**. The bar chart never changed: Sydney was sitting there at **24%** the whole time.
> The model didn't glitch on the second run; you rolled its dice, and one time in four they come
> up Sydney.

The secondary beat: switch to the prompt "Last quarter our revenue grew by". Every candidate is a
crisp, confident number: 8%, 12%, 15%. None is grounded in anything; the distribution is smooth
over fabrications. A peaked distribution *feels* like knowledge ("2 + 2 =" shows 4 at 97%), a flat
one *feels* like opinion, and the machinery is identical in both.

## 2. Screen layout

Single column, chart dominant:

1. **The prompt picker**: five prompt buttons (§3), the selected prompt shown in monospace with
   the sampled continuation appended token by token.
2. **The distribution**: a horizontal bar chart of the candidate next tokens, bars labelled with
   token and percentage, sorted by probability. The bar that was just sampled flashes; bars are
   `--system` purple, the sampled one outlined in `--accent`.
3. **The controls**: **Temperature** slider (0 to 2, default 1) with three worded zones
   (deterministic / balanced / adventurous); **Sample** button; **Run again** (resets the
   continuation and replays from the same starting seed sequence).
4. **The verdict band**: after each sample, one sentence naming what happened ("picked the 2nd
   most likely token; 24% chances happen one time in four").

Closing card: *the model always does exactly this, thousands of times per answer. Confidence in
the prose is a property of the sampling, not of the truth. That is why you verify the
load-bearing claims (the trust tool's whole argument), and why the same prompt gives your
classmate a different answer.*

## 3. The prompts and distributions

Five prompts, each with a hand-authored table of 8–12 candidate tokens and **logits** (not
probabilities; the temperature math needs logits, §4). Continuations are shallow trees: each
sampled token leads to either a follow-on distribution (max depth 3) or a fixed completion, all
hand-authored.

| Prompt | Distribution shape | Teaches |
|---|---|---|
| "The capital of Australia is" | Canberra 62%, Sydney 24%, Melbourne 9%, tail | the moment (§1) |
| "2 + 2 =" | "4" at 97% | peaked ≈ what "knowing" feels like |
| "Last quarter our revenue grew by" | smooth over 8 plausible numbers | confident fabrication |
| "The best programming language is" | near-flat over 9 names | flat ≈ what "opinion" feels like |
| "The opposite of up is" | "down" 91%, then jokes in the tail | tails exist even when it's easy |

Percentages above are at temperature 1; they are the softmax of the stored logits, so the
displayed chart is always *computed*, never hard-coded.

## 4. The math and the seed

- Displayed distribution: `p_i = softmax(logit_i / T)`. At T→0 the top token goes to 100%
  (implement as argmax below T=0.05 and label the zone "deterministic"). At T=2 the chart
  visibly flattens. The bars re-animate as the slider moves: temperature *reshapes*, it does not
  add magic.
- Sampling uses seeded mulberry32, **seed 7**, consumed in sequence. The seed and the Canberra
  logits are chosen together so that, at T=1, **draw 1 lands on Canberra and draw 2 lands on
  Sydney**. Every student sees the same two runs; the demo repeats on the projector. Verify this
  pair at build time and freeze it.
- **Run again** resets the PRNG to the start-of-prompt state, so a full replay is identical;
  the "different answer" comes from consecutive draws, never from hidden randomness.

## 5. Deliberate simplifications

- **Words as tokens.** Real tokenisers split subwords; one closing-card sentence, no mechanic.
- **Hand-authored logits.** No real model runs; the page carries the standard footer ("No AI runs
  on this page; the distributions are illustrative").
- **No top-k / top-p.** One knob (temperature) is enough to teach reshaping; samplers get a
  passing mention in the closing card.
- **Shallow trees.** Real generation is thousands of steps; three is enough to show compounding
  choice.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: seeded sampling, computed softmax, identical on every load.
- Temperature is a native `range`; prompt buttons carry `aria-pressed`; each sample's outcome is
  announced via `role="status" aria-live="polite"` ("Sampled: Sydney, 24% likely").
- The sampled bar is marked by outline *and* the word SAMPLED, not colour alone.
- Monospace for tokens and the growing sentence only; prose stays in the UI font.
- `?present` mode; works from `file://`, at 1024×768, and on a phone.
