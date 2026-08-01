# K-Anonymity: build specification

Sufficient to rebuild the toy from scratch.

---

## 1. The one thing it teaches

**Removing names is not anonymisation.** A record is identified by the combination of ordinary
attributes it carries, and anonymity is purchased by coarsening those attributes until the record
hides in a crowd of k; the purchase price is analytic precision.

**The "wait, what?" moment**:

> The table opens with a green "Names removed ✓" banner and **seven rows glowing red**. The k
> readout says **k = 1: seven customers are one search away**. Click the reddest row: a mock
> search-result snippet appears: *"51-year-old CFO, mining company, Karratha"; there is exactly
> one of those people.* The nameless table just named her.

The secondary beat: coarsen until **k = 5** and the question card flips to UNANSWERABLE. The
question "Which city should get the new support hub?" died when City became State. Privacy and
precision are the same dial turned opposite ways; the skill is choosing the trade, not pretending
there isn't one.

## 2. Screen layout

Single main column, controls above the table:

1. **The banner**: "Names removed ✓" in `--good` green, deliberately smug; it stays visible while
   the k readout beside it contradicts it. The juxtaposition is the toy's thesis.
2. **The dials**: one stepper per quasi-identifier column (see §3), each showing its current
   generalisation level in words ("Age: exact → 10-year band").
3. **The table**: 20 fixed rows. Rows in an equivalence class of size 1 glow `--bad` red with the
   word SINGLED OUT; size 2–4 `--warn` amber, AT RISK; size ≥5 neutral, IN A CROWD of N.
4. **The readouts**: big **k = smallest class size**; a utility meter ("this table can still
   distinguish N distinct groups"); the **question card** ("Which city should get the new support
   hub?" → ANSWERABLE / UNANSWERABLE); the verdict band.

Closing card: *de-identified is not anonymised; k-anonymity is a floor, not a guarantee (linkage,
homogeneity and background knowledge still bite); and the analyst who coarsened the data chose
what questions the business may still ask.*

## 3. The data

Twenty hand-authored rows, fixed constants, no PRNG. Support tickets for a nameless B2B firm.
Columns: Ticket, Age, Role, City, Industry, Revenue, plus a free-text Issue column that is never
generalised (it quietly demonstrates that free text leaks too; one issue mentions "our Karratha
site office").

Generalisation ladders (each stepper walks one ladder; final rung is suppression):

| Column | Rung 0 (exact) | Rung 1 | Rung 2 | Rung 3 |
|---|---|---|---|---|
| Age | 51 | 5-year band | 10-year band | suppressed |
| Role | CFO | function (Finance / IT / Ops / Exec) | suppressed | |
| City | Karratha | state (WA / NSW / VIC / QLD) | suppressed | |
| Industry | Mining | sector (Resources / Services / Retail) | suppressed | |
| Revenue | $48M | band ($1–10M / $10–50M / $50M+) | suppressed | |

Author the 20 rows so that, verified by hand:

- at all-exact settings, exactly **7 rows are singletons** (k=1), including the Karratha CFO;
- the Karratha CFO stays a singleton until City reaches state level **and** Role reaches
  function level (she is the toy's through-line; her row id is stable);
- **k = 5 is reachable** with a mixed setting that suppresses nothing (bands + state + sector),
  so the lesson is "coarsen", not "delete";
- suppressing everything gives k = 20 and a utility meter at zero: the degenerate end of the dial.

## 4. Derived numbers

- Equivalence classes are computed live: group rows by the tuple of currently-displayed values.
- `k = min(class size)`; the readout names the smallest class's rows, not just the number.
- Utility meter = count of distinct tuples (20 at full precision, 1 at full suppression), with a
  word label, because a bare percentage would slide off.
- The question card is ANSWERABLE while City is at rung 0 and at least two cities have ≥2 rows;
  the rule is computed from the table, not hard-coded.
- The mock search snippet for a clicked singleton is assembled from the row's current displayed
  values; as the values coarsen, the snippet visibly degrades ("a 45–55 year old in WA" matches
  thousands), which is re-identification risk made legible.

## 5. Deliberate simplifications

- **k-anonymity only.** No l-diversity or t-closeness; the homogeneity attack gets one closing-card
  sentence, not a mechanic.
- **Twenty rows.** Real tables have millions; small enough to read is the point of a toy.
- **One table, no join.** Real linkage attacks join an external dataset; the mock search snippet
  stands in for that entire class of attack.
- **The utility meter is a proxy.** Real analytic loss depends on the query workload.

## 6. Non-negotiables

- Single self-contained `index.html`. No CDN, no fonts, no fetch, no storage, no analytics.
- All 20 rows are constants in the source; identical on every load.
- Steppers are native controls (`range` or button pairs) with the current rung announced in words;
  k readout and question card in `role="status" aria-live="polite"`.
- SINGLED OUT / AT RISK / IN A CROWD carried by words, not only row colour.
- No real person and no real employer is describable from the table: fictional combinations only,
  checked against the obvious searches before ship.
- `?present` mode; works from `file://`, at 1024×768, and on a phone (table scrolls inside its own
  container).
