# K-Means Stepper: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**The clusters are not in the data.** K-means always converges and always sounds certain; which
partition it hands you depends on choices a human made first (how many clusters, where the search
started). A cluster is an artefact until a person names it and the name survives scrutiny.

**The "wait, what?" moment**:

> Run k=3 from **Start A** to convergence: the banner says **"Converged: nothing moved ✓"** and
> the middle clump of documents belongs to the left cluster (distance 6.18). Press **Start B**,
> same k, same data, run again: **"Converged ✓"** again, distance 6.26, and **ten documents have
> changed allegiance**: the clump now belongs to the right cluster. The algorithm is equally
> proud of both answers. It has no opinion; it has a stopping condition.

The secondary beat: set **k=5**. The algorithm happily shatters one coherent blob into two
"segments" and converges with its usual confidence. Nothing in the output says "too many"; the
machine answers *"how do these divide into five?"*, never *"are there five?"*. Choosing k is the
analyst's claim about the world, not the algorithm's discovery.

## 2. Screen layout

Two columns, collapsing below 860px:

1. **The map**: a canvas scatter of 60 points ("60 support tickets, plotted so similar tickets
   sit near each other"). Centroids are large × marks; each point takes its centroid's colour on
   Assign; during Assign, faint spokes flash from each point to its centroid; on Move, centroids
   glide to their cluster's mean. Points that changed cluster since the previous iteration pulse
   with a ring and are counted.
2. **The controls**: **k** buttons (2 / 3 / 4 / 5); **Start A / Start B / Start C** (fixed
   initialisations per k, §3); a **Step** button whose label alternates *Assign points* /
   *Move centroids*; **Auto-run**; **Reset**.
3. **The readouts**: iteration count; "points that switched: N"; **total distance** (sum of
   point-to-centroid distances, the algorithm's own score, labelled "what the algorithm is
   minimising"); the convergence banner; the verdict band. After convergence from two different
   starts at the same k, the verdict compares them: "Start A: distance 6.18. Start B: distance
   6.26. Both converged. **They disagree on 10 of 60 tickets.**" (figures frozen from build).

Closing card: *Orange will hand you this converged answer in one widget with no stepping and no
second start. Now you know what it did, what it optimised, and what it never checked. The insight
starts when a human looks at a cluster and tries to name it; if the name doesn't survive contact
with the points, the cluster was arithmetic, not meaning.*

## 3. The data and the starts

Sixty 2-D points, generated once with seeded mulberry32 (**seed 53**, regenerated at runtime
from the same seed, params and draw order). The shape is engineered, because the ambiguity is
the teaching case; note the contested mass is a **compact midway clump**, not a strung-out
bridge (a thin string always cascades into one basin: its edge points defect, the centroid
drifts, and the rest follow; verified the hard way):

- **Blob P** (18 points) at μ=(0.18, 0.70), σ=0.05;
- **Blob Q** (20 points) at μ=(0.78, 0.70), σ=0.05;
- **The clump** (10 points) at μ=(0.48, 0.70), σ=0.03, whose allegiance is bistable at k=3;
- **Blob R** (12 points) at μ=(0.50, 0.18), σ=(0.06, 0.05); all coordinates clamped [0.04, 0.96].
- Generation order is P, Q, clump, R (x then y per point); changing it changes the dataset.

Verified at build (rebuilds must reproduce): k=3 **Start A converges in 3 iterations to
(P+clump 28, Q 20, R 12), total distance 6.18**; **Start B converges to (P 18, clump+Q 30,
R 12), distance 6.26** (1.2% apart); the two partitions **disagree on 10 of 60 points**
(best-permutation matching). Start C converges with Start A's basin (three starts so "best of
several restarts" is demonstrable: the honest practitioners' fix, named in the closing card).

Starts are **fixed centroid coordinates per (k, start)**, never sampled at runtime, but they are
deliberately **scattered in empty space, never on a blob** — an earlier build parked them on the
blob centres, which leaked the answer and converged before students saw any travel. Each shipped
set was sampled once with a seeded PRNG and kept only if every centroid sat ≥0.16 from every
blob centre and ≥0.20 from its siblings, yet still converged (in 3–7 Assign steps, so the journey
is visible) to the verified partitions above. The k=3 pair that produces the two truths:
A = (0.22,0.12), (0.33,0.48), (0.88,0.25); B = (0.63,0.91), (0.75,0.12), (0.12,0.10);
C = (0.83,0.10), (0.38,0.49), (0.93,0.30).
For k=5, Start A splits Q (9+11), Start B splits P (9+9); k=4 Start B splits R (7+5). All 12
shipped (k, start) combinations verified to converge with no empty clusters.

## 4. The algorithm, honestly

- Standard Lloyd's iterations, computed live: Assign = nearest centroid by Euclidean distance;
  Move = centroid to the mean of its assigned points. No faked animations: the positions are
  the arithmetic.
- Convergence = no point changed assignment on the last Assign. Banner fires then, and the Step
  button disables with the label "Nothing will move".
- An empty cluster (unreachable from shipped starts, but guard anyway) keeps its centroid in
  place and the verdict names it: "an empty segment: the algorithm doesn't mind; do you?"
- Auto-run steps at ~700ms per half-step so the glide is followable; honours
  `prefers-reduced-motion` by jumping between states.

## 5. Deliberate simplifications

- **2-D points because eyes.** Real document vectors have hundreds of dimensions; "plotted so
  similar tickets sit near each other" is doing the work a real pipeline does with embeddings
  plus projection, and the closing card says so.
- **Three fixed starts, not random restarts.** Real practice runs many random starts and keeps
  the best distance; fixed starts keep the disagreement reproducible on a projector. The fixed
  starts are scattered in empty space (§3), so they look and behave like careless random inits
  without giving up that reproducibility.
- **Euclidean distance**, while text pipelines mostly use cosine on normalised vectors; the
  behavioural lesson is identical.
- **No elbow plots or silhouette scores.** They exist and help; they also just move the human
  judgement one level up, which the closing card notes in one sentence.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: frozen points, fixed starts, identical partitions and distances every load.
- k and start selectors carry `aria-pressed`; Step/Auto are buttons; iteration outcomes are
  announced via `role="status" aria-live="polite"` ("Assign: 11 tickets switched"), once per
  step, not per point.
- Cluster identity is carried by colour *and* point shape (circle / square / triangle / diamond
  / cross), because four-plus colours alone fail the accessibility floor; CONVERGED is a word.
- Canvas has an offscreen text summary per state for screen readers.
- `?present` mode; works from `file://`, at 1024×768, and on a phone.
