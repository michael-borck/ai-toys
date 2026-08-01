# K-Means Stepper

Kills: **"the clusters are in the data."**

Step Lloyd's algorithm by hand over sixty documents: Assign, Move, repeat until nothing changes.
Start A converges proudly (distance 6.18) with the middle clump in the left cluster; Start B
converges just as proudly (6.26) with the clump on the right; ten documents change allegiance
between two answers the algorithm is equally certain of. Set k=5 and watch it shatter a coherent
blob without complaint: it answers "how do these divide into k?", never "are there k?".

Open `index.html`. Add `?present` for a projector. See `SPEC.md` to rebuild it from scratch.
