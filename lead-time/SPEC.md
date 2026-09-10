# Lead Time: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Lead-time bias.** Early detection moves the *diagnosis* earlier without moving the *death*,
so every "survival after diagnosis" statistic improves automatically. Survival rates measure
where you started counting; the mortality rate (are fewer people dying of the disease?) is the
statistic that can actually prove a screening programme works.

**The "wait, what?" moment**

> Drag "screening starts" from 2029 back to 2007: mean survival after diagnosis climbs from
> ~4.8 to ~9.8 years and the 5-year survival rate goes 50% → 100%, while the death card reads
> "2012 – 2030" — unchanged. Nobody lived longer. We started counting earlier.

## 2. Data (frozen, no randomness)

Twelve patients as [disease onset, disease duration]; death = onset + duration; symptom
diagnosis = onset + 5 years; everyone alive from 2000 (schematic):

(2006,6) (2008,14) (2009,8) (2010,12) (2011,7) (2012,13) (2013,9) (2014,11) (2015,6)
(2016,14) (2017,8) (2018,10) → deaths 2012–2030.

Screening rule, for programme start year S:
- S ≤ onset → diagnosed at onset (caught at the first screen)
- onset < S < death → diagnosed at S (caught at the first screen after onset)
- S ≥ death → symptomatic diagnosis at onset + 5 (programme came too late to matter)

Verified headline numbers: S = 2029 (effectively none) → mean survival ≈ 4.8 yrs, 5-year
survival 50%; S = 2007 → mean ≈ 9.8 yrs, 100%. Deaths 2012–2030 in both worlds.

## 3. Mechanics

- Slider: screening start year 2007–2029, default 2029.
- SVG lifelines (1000×330, years 2000–2040): each patient is a grey life bar with a teal
  overlay on the diagnosis→death span (what gets *counted* as survival), an amber tick at
  disease onset, a red death dot. A dashed amber vertical marks the programme start.
- Live stats: average survival after diagnosis, 5-year survival rate (the screening headline),
  and the death-years card that never changes.
- No randomness, no storage, no network.

## 4. Layout

- Single 1020px column: header, slider card (chart + legend + stats), wait-what card.
- All sizes em-based off `--fs` (16px; `?present` → 20px).

## 5. Accessibility floor

- Slider is native with a live label; the SVG carries role="img" + aria-label that states the
  invariance in words ("every death date stays exactly where it was").
- The three headline numbers are text; the debrief card carries the whole argument in prose,
  including the length-bias cousin: screening also preferentially catches slow disease
  (mention in debrief prose if expanded; one idea per toy — lead time is the core here).
