# Pattern Spotter: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**"The data speaks for itself" is false.** Every small dataset here supports at least two
confident readings; picking between them requires context the chart does not contain. The
student experiences interpretation doing silent work — before any algorithm is involved.

**The "wait, what?" moment**

> Exercise 2. "Seasonal drift" is a genuinely plausible reading of a steady six-month decline,
> and the toy says so. The chart cannot settle season-vs-structure; deciding the *story* by
> confidence alone is the trap. (Exercise 1 sets it up: all four bars look alike until you
> compare pairs; exercise 3 lands the ratio-vs-volume read.)

## 2. Content (three exercises, each: chart + one question + three options, "b" = best read)

1. **Four quarters, four regions** — CSS bar chart: North $78.9K / South $91.2K / East $74.5K /
   West $98.7K. Best read: a geographic divide (West/South clear North/East by 15–25%).
   Plausible decoys: "revenue consistency", "random variation".
2. **Six months of satisfaction** — SVG line: Jan 8.2, Feb 8.3, Mar 7.8, Apr 7.2, May 6.8,
   Jun 6.2, monotonic decline. Best read: a systematic problem. Plausible decoys: "seasonal
   drift" (explicitly acknowledged as plausible in the feedback — that IS the lesson) and
   "market maturation".
3. **Support load by segment** — three stat cards: Enterprise 2.1 tickets/mo, 6 h resolution;
   SME 4.7 / 18 h; Education 8.3 / 32 h. Best read: the ratio, not the volume — segments need
   different service. Plausible decoys: "hire where the volume is", "charge to cover cost".

Final card names the mechanism: the numbers never argued; interpretation did the work; machines
that find patterns inherit your framing; cross-link to `k-means-stepper`.

## 3. Mechanics

- One exercise visible at a time (`hidden` attribute), progress bar (0/33/66/100%).
- Options are `<button class="opt" data-k="a|b|c">` — "b" is the best read in every exercise
  (deliberately not shuffled: position is not the lesson, the reading is). Click marks the
  best option `.good`, a wrong pick `.badpick`, disables the group, reveals feedback, and
  enables Next. Answering wrong still advances — the feedback IS the content.
- Feedback per exercise per outcome lives in a `FB` object; the wrong-pick text does the
  "wait, what?" work by taking the decoy seriously.
- No timer, no storage, no randomness, no alerts.

## 4. Layout

- Single 880px column: header, progress bar, three exercise cards, final card.
- Charts: CSS flex bar chart (fixed 150px plot height), inline SVG line chart
  (viewBox 0 0 420 230), CSS grid stat cards. All values visible as text — colour never the
  only signal.
- All sizes em-based off `--fs` (16px; `?present` → 20px). Options stack under 520px.

## 5. Accessibility floor

- Options are real `<button>`s (keyboard + focus ring free); feedback is
  `role="status" aria-live="polite"`; the SVG line chart carries `role="img"` + aria-label
  stating the trend in words; every verdict is worded, not just coloured.
