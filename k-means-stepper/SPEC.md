# K-Means Stepper: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**The clusters are not in the data.** K-means always converges and always sounds certain; which
partition it hands you depends on choices a human made first (how many clusters, where the search
started). A cluster is an artefact until a person names it and the name survives scrutiny.

**The "wait, what?" moment**:

> Run k=3 from **Start A** to convergence: the banner says **"Converged: nothing moved ✓"** and
> the bridge documents belong to the left cluster. Press **Start B**, same k, same data, run
> again: **"Converged ✓"** again, similar total distance, and **eleven points have changed
> allegiance**: the bridge now belongs to the right cluster. The algorithm is equally proud of
> both answers. It has no opinion; it has a stopping condition.

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
   starts at the same k, the verdict compares them: "Start A: distance 7.91. Start B: distance
   8.03. Both converged. **They disagree on 11 of 60 tickets.**" (freeze exact figures at build).

Closing card: *Orange will hand you this converged answer in one widget with no stepping and no
second start. Now you know what it did, what it optimised, and what it never checked. The insight
starts when a human looks at a cluster and tries to name it; if the name doesn't survive contact
with the points, the cluster was arithmetic, not meaning.*

## 3. The data and the starts

Sixty 2-D points, generated once with seeded mulberry32 (**seed 53**) and frozen as constants in
the source. The shape is engineered, because the ambiguity is the teaching case:

- **Blob P** (18 points) top-left, compact;
- **Blob Q** (20 points) right, compact;
- **Blob R** (14 points) bottom-centre, slightly loose;
- **The bridge** (8 points) strung between P and Q, spacing tuned so k=3 has two basins of
  attraction: one assigning the bridge with P, one with Q.

Verify at build, then freeze: Start A and Start B at k=3 must converge to partitions differing
on ≥10 points with total distances within 5% of each other; if seed 53 doesn't deliver that,
adjust the bridge and re-freeze. Start C converges with the majority basin (three starts so
"best of several restarts" is demonstrable: the honest practitioners' fix, named in the closing
card).

Starts are **fixed centroid coordinates per (k, start)**, hand-placed, never sampled at runtime:
12 small coordinate tables in the source. For k=5, Start A splits Blob Q; Start B splits Blob P;
the shattering must look clean, not degenerate (no empty clusters from any shipped start).

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
  the best distance; fixed starts keep the disagreement reproducible on a projector.
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
