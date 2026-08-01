# Next Token

Kills: **"the model knows things."**

Pick a prompt and watch the model weigh every candidate next token, then roll seeded dice over
the weights. Run 1 says Canberra; run 2 says Sydney, and the bars never moved: Sydney was always
sitting there at 24%. The revenue prompt shows the same machinery producing confident numbers
with no ground truth anywhere in them, which is all "hallucination" ever was. A temperature
slider reshapes the weights honestly via softmax.

No AI runs on the page; the distributions are hand-written and the rolls are seeded, so every
class sees the same two runs.

Open `index.html`. Add `?present` for a projector. See `SPEC.md` to rebuild it from scratch.
