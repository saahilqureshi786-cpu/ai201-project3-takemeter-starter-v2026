# Acceptance Criteria — TakeMeter

These five criteria define what I would consider a useful TakeMeter classifier
for the r/nba taxonomy before looking at any training results.

The classifier uses four labels:

- `analysis`
- `hot_take`
- `reaction`
- `question`

With four labels, a naive random prediction would be correct only about 25% of
the time, so my targets are intentionally well above that baseline.

---

## 1. Overall accuracy must be at least 70%

Across the held-out test set, the trained classifier should achieve an overall
accuracy of at least **70%**.

**Why this target:**

With four labels, random guessing would be around 25%, so 70% would show that
the model learned meaningful distinctions in r/nba discussion rather than
mostly guessing. I do not expect near-perfect accuracy because boundaries such
as `analysis` versus `hot_take` can genuinely be ambiguous.

---

## 2. Macro F1 must be at least 0.65

The classifier should achieve a **macro F1 score of at least 0.65** on the
held-out test set.

**Why this target:**

Overall accuracy can hide weak performance on a smaller label. Macro F1 gives
each of my four labels equal importance, which matters because I want TakeMeter
to recognize questions and reactions as well as the more common opinion-based
posts.

---

## 3. Every individual label must have F1 of at least 0.55

Each of the four labels — `analysis`, `hot_take`, `reaction`, and `question` —
should achieve an **F1 score of at least 0.55** on the held-out test set.

**Why this target:**

The hardest distinctions in this taxonomy are likely to be `analysis` versus
`hot_take` and `reaction` versus `hot_take`. I am therefore setting the
per-label floor slightly below the macro-F1 target. An F1 below 0.55 would
suggest that at least one category is not being learned reliably enough to be
useful.

---

## 4. No label should make up more than 40% of the dataset

After the initial dataset is complete, **no single label should represent more
than 40% of all labeled examples**.

**Why this target:**

There are four labels, so an exactly balanced dataset would place each near
25%. I do not expect perfectly equal counts because r/nba naturally contains
more opinions and reactions than some other post types, but allowing one label
to exceed 40% would make the dataset noticeably lopsided and could encourage
the model to favor that class.

---

## 5. Human agreement should be at least 80%

When another person applies the written taxonomy and decision rules to the same
posts, I should agree with their labels on at least **80% of the examples**.

**Why this target:**

TakeMeter can only learn a distinction consistently if the label definitions
are clear enough for different people to apply similarly. The boundaries in
this project depend on whether a post provides real supporting evidence, is
mainly reacting to an event, or is genuinely asking a question. An agreement
rate of at least 80% would indicate that those rules are clear enough to be
useful while still allowing for some genuinely ambiguous basketball posts.

---

## Criteria Summary

| # | Measurement | Target |
|---|---|---:|
| 1 | Overall accuracy | ≥ 70% |
| 2 | Macro F1 | ≥ 0.65 |
| 3 | F1 for every individual label | ≥ 0.55 |
| 4 | Maximum share of any one label | ≤ 40% |
| 5 | Human labeling agreement | ≥ 80% |

These targets were selected before examining the trained model's results.
If a target is missed later, I will report it as missed rather than lowering
the target after seeing the results.