# Overfit: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Training accuracy is a rigged exam.** Add complexity and the score on data the model has seen
climbs toward 100% while the score on data it hasn't falls; the model has stopped learning the
pattern and started memorising the noise. Never test on what you trained on.

**The "wait, what?" moment**:

> Drag complexity to maximum: **train accuracy 100%**, and the decision boundary has grown little
> islands, each fitted around a single stray point. Press **Show test data**: **test accuracy
> 73%**, worse than the modest setting's 87%. The islands are memorised noise, and every one of
> them now misclassifies the fresh points that landed there.

The secondary beat: the **train curve alone would have told you to max the slider**. Both curves
plot side by side, and the toy makes the student watch the advice diverge: the rigged exam says
"more", the honest exam says "stop". This is why a held-out split is the first thing Module 11's
core loop does, and why Module 4's Orange workflow keeps Data Sampler upstream of Test & Score.

## 2. Screen layout

Two columns, collapsing below 860px:

1. **The map**: a canvas scatter of two classes (teal circles, purple squares) with the decision
   boundary painted as tinted regions. Toggle buttons: **Training data / Test data / Both**
   (test points render hollow). Misclassified points under the current view get a red ring and
   count.
2. **The dial and the curves**: the **Complexity** slider (7 stops, §3); a line chart of train
   and test accuracy versus complexity, both full curves precomputed and drawn from the start,
   with a marker at the current stop; big paired readouts (TRAIN 100% / TEST 73%) whose gap is
   also stated in words ("the model is 27 points better on the exam it wrote itself").
3. **The verdict band**: names the current regime: underfit ("too simple for either set"),
   sweet spot ("best score on data it hasn't seen"), overfit ("memorising noise").

Closing card: *the gap between the two numbers is the overfit, the honest number is always the
test number, and if someone quotes one accuracy figure, ask which data it was measured on.*

## 3. The data and the model

Two classes in 2-D, generated once with seeded mulberry32 (**seed 17**) and then treated as
frozen: **60 training points and 60 test points** drawn from the same two overlapping Gaussian
blobs (class A: μ=(0.35, 0.62); class B: μ=(0.65, 0.40); shared σ=0.13, clamped to [0.05, 0.95]),
plus **6 training-only label-noise points** (drawn inside the *other* class's blob) whose indices
are frozen constants. The noise points are the future islands; sampling them live risks a tame
draw with no islands, and the islands are the moment.

The classifier is **k-nearest-neighbour**, honestly computed in-page; the complexity slider is
inverted k across seven stops: **k = 31, 21, 15, 9, 5, 3, 1** (left = simplest). kNN is chosen
because the boundary is real (no faked artwork), cheap (120 points), and k=1 reliably produces
one island per noise point.

Boundary painting: classify a 90×70 grid of the unit square against the training set, tint each
cell by predicted class at low alpha. Recompute on slider move; 6,300 kNN queries against 66
points is instant.

Tune σ and the noise placement at build time so the frozen dataset yields, verified: train
accuracy monotone rising to 100% at k=1; test accuracy peaking at k=9 ± one stop (≈87%) and
falling to ≈73% at k=1. Freeze the verified numbers into this spec on first build.

## 4. Derived numbers

- Train accuracy at stop s = fraction of the 66 training points (including noise) correctly
  classified by leave-nothing-out kNN over the training set. (k=1 therefore scores 100% by
  construction: every point is its own nearest neighbour. That artefact *is* the rigged exam,
  and the verdict band says so.)
- Test accuracy = fraction of the 60 test points classified correctly against the training set.
- Both full curves are computed once at load (7 stops × 126 points), so the chart never waits.
- The sweet-spot verdict fires at whichever stop maximises test accuracy; computed, not
  hard-coded.

## 5. Deliberate simplifications

- **One dataset, one split.** Cross-validation is the real practice; one held-out set is the
  minimum that shows the divergence.
- **kNN stands in for every model.** Depth in trees, degree in polynomials, epochs in networks:
  same curve, different dial; the closing card says so.
- **2-D because eyes.** Real feature spaces have hundreds of dimensions and no picture; the
  memorised-noise geometry is the same.
- **Six noise points is generous.** Real label noise is subtler; six guarantees visible islands
  on a 60-point canvas.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- Deterministic: frozen dataset, frozen noise indices, identical boundary every load.
- Complexity is a native `range` with 7 stops; view toggles carry `aria-pressed`; the paired
  accuracy readout is `role="status" aria-live="polite"`.
- Classes differ by shape (circle/square) as well as colour; misclassification is a ring *and*
  a count in words; UNDERFIT / SWEET SPOT / OVERFIT are words in the verdict band.
- Canvas has an offscreen text summary updated per stop for screen readers.
- `?present` mode; works from `file://`, at 1024×768, and on a phone.
