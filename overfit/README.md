# Overfit

Kills: **"higher training accuracy means a better model."**

One slider makes a nearest-neighbour model more flexible. Train accuracy climbs to 100% while
test accuracy falls to 73.3%, and the boundary grows an island around every stray point: memorised
noise, each moat now misclassifying the fresh points that landed in it. The two curves plot side
by side so you can watch the rigged exam's advice point exactly the wrong way.

The model is real (kNN computed live in the browser); the data is frozen so the curves are the
same in every class.

Open `index.html`. Add `?present` for a projector. See `SPEC.md` to rebuild it from scratch.
