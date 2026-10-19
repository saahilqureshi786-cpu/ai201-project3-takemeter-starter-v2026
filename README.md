# TakeMeter

TakeMeter is a local text-classification project built for CodePath AI201. It
classifies public posts from **r/nba** into four discussion types:

- `analysis`
- `hot_take`
- `reaction`
- `question`

The classifier is fine-tuned from `distilbert-base-uncased` in
`takemeter.ipynb`.

---

## What This Does

TakeMeter sorts r/nba posts by the main purpose of the post rather than by
whether the post is "good" or "bad."

The four labels capture whether a post:

- supports a basketball claim with evidence or reasoning,
- makes a confident unsupported claim,
- mainly reacts to an event,
- or mainly asks the community something.

This gives the classifier a concrete, observable taxonomy to learn.

---

## Label Taxonomy

### `analysis`

**Definition:** A post that makes a basketball claim and supports it with a
specific statistic, checkable fact, comparison, example, or concrete chain of
reasoning.

**Example 1:**

> Is Lebron James a bigger global star than Virat Kohli? Virat Kohli: 271
> Million Instagram followers. Lebron James: 153 Million Instagram followers.

**Example 2:**

> Ben Simmons good again? ... he showed out in the preseason ... shooting mid
> ranges and looking confident ... showing off his playmaking ability.

### `hot_take`

**Definition:** A post that confidently states an opinion, prediction, ranking,
or conclusion without providing meaningful evidence or reasoning to support
the claim.

**Example 1:**

> Why the Spurs run wasn’t legit and Minnesota is the better team

**Example 2:**

> LeBron, Giannis and Kevin Durant should be kept away from children.

### `reaction`

**Definition:** A post whose main purpose is an immediate emotional response
to a game, player performance, trade, highlight, injury, announcement, or other
recent basketball event rather than an argument.

### `question`

**Definition:** A post whose main purpose is to ask the community for an
explanation, comparison, prediction, recommendation, or opinion instead of
primarily asserting its own conclusion.

## The Dataset

**Source:** Public posts from **r/nba**. I collected a 300-post working pool
from a public Reddit archive and built the final training dataset from 200
unique reviewed posts.

### Final counts

| Label | Count | Share |
|---|---:|---:|
| `analysis` | 37 | 18.5% |
| `hot_take` | 24 | 12.0% |
| `reaction` | 87 | 43.5% |
| `question` | 52 | 26.0% |
| **Total** | **200** | **100%** }

## The Training Run

```text
Epochs:         3
Learning rate:  2e-5
Batch size:     16
Maximum length: 128
Seed:           42
Split:          70% train / 15% validation / 15% test
```

### Results

```text
Accuracy:  0.433
Macro F1:  0.151
```

| Label | F1 | Test support |
|---|---:|---:|
| `analysis` | 0.000 | 5 |
| `hot_take` | 0.000 | 4 |
| `reaction` | 0.605 | 13 |
| `question` | 0.000 | 8 |

## How I Used AI

- Rows 1-20: manual/cold labels, no AI pre-labeling.
- Remaining reviewed rows: AI suggested a preliminary label.
- I read each post and accepted or corrected the suggestion.
- The `note` column records whether a row was cold, AI-Reviewed, or
  AI-corrected.

## Stretch Status

**Fourth label — completed.* The `question` label is a genuine category in the
dataset.

**Second training run — not completed in this submission.**

**50 extra examples for the thinnest label — not completed in this
submission.**

## Unit 5 Files

* `criteria.md`
* `labels.csv`
* `results.json`
* `test_split.csv`
* `label_definitions.txt`
* `takemeter.ipynb`
* `README.md`
