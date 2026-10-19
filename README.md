# TakeMeter

TakeMeter is a text classifier that sorts posts from a public online community
into a small set of clearly defined categories.

For this project I am using posts from the public **r/nba** community. The
classifier is intended to distinguish between posts that make supported
basketball arguments, unsupported takes, immediate reactions, and questions.

The project runs completely on my local machine. The model is fine-tuned in
`takemeter.ipynb`, and the notebook writes the training results back into this
repository.

> **Setup and commands:** See [RUNNING.md](RUNNING.md).

---

# Unit 5 — Build

## Unit 5 Stretch Features — Declared Before Build

Before implementing the stretch work, I plan to attempt all three Unit 5
stretch features.

1. **Fourth label:** I will use a fourth label when the community contains a
   meaningful type of post that is not cleanly covered by the first three
   labels.

2. **Second training run:** After the initial model is trained, I will perform
   a second training run with exactly one hyperparameter changed and compare
   the results against the first run.

3. **50 additional examples for the thinnest label:** After completing the
   initial dataset, I will identify the label with the fewest examples and add
   at least 50 additional examples for that label. I will record where those
   examples came from and what happened to that label's performance.

These stretch features were declared before they were implemented.

---

## What This Does

TakeMeter classifies public posts from **r/nba** according to the kind of
contribution the post is making.

The goal is not to decide whether a post is "good" or "bad." Instead, the
classifier looks for observable differences in how basketball discussions are
written.

A post can be classified as:

- `analysis`
- `hot_take`
- `reaction`
- `question`

The distinction is based on whether the post provides support for a claim,
asserts an unsupported opinion, reacts primarily to an event, or asks the
community for information or an opinion.

---

## Label Taxonomy

### `analysis`

**Definition:** A post that makes a basketball claim and supports it with a
specific statistic, checkable fact, comparison, example, or concrete chain of
reasoning.

**Example 1:**

> TODO: Paste a real r/nba post that clearly makes a supported basketball
> argument.

**Example 2:**

> TODO: Paste a second real r/nba post that clearly fits `analysis`.

---

### `hot_take`

**Definition:** A post that confidently states an opinion, prediction, ranking,
or conclusion without providing meaningful evidence or reasoning to support
the claim.

**Example 1:**

> TODO: Paste a real r/nba post containing a confident unsupported take.

**Example 2:**

> TODO: Paste a second real r/nba post that clearly fits `hot_take`.

---

### `reaction`

**Definition:** A post whose main purpose is an immediate emotional response
to a game, player performance, trade, highlight, injury, announcement, or other
recent basketball event rather than an argument.

**Example 1:**

> TODO: Paste a real r/nba reaction post.

**Example 2:**

> TODO: Paste a second real r/nba reaction post.

---

### `question`

**Definition:** A post whose main purpose is to ask the community for an
explanation, comparison, prediction, recommendation, or opinion instead of
primarily asserting its own conclusion.

**Example 1:**

> TODO: Paste a real r/nba question post.

**Example 2:**

> TODO: Paste a second real r/nba question post.

---

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The decision rule I used every time:**

If a post makes a claim and gives at least one specific, checkable fact,
statistic, concrete comparison, or clear piece of reasoning that supports that
claim, I label it `analysis`, even if the tone is strong or argumentative.

If the post mainly states the conclusion confidently but does not provide
meaningful support for it, I label it `hot_take`.

A statistic does not automatically make something analysis if it is unrelated
to the argument. The evidence has to actually support the claim being made.

### Secondary decision rules

**`reaction` vs. `hot_take`:**

If the main purpose of the post is expressing an immediate emotional response
to something that just happened, I use `reaction`. If the post is trying to
make a broader judgment, prediction, ranking, or claim that extends beyond the
immediate moment, I use `hot_take` unless it contains enough supporting
reasoning to become `analysis`.

**`question` vs. another label:**

If the main communicative purpose is genuinely asking the community something,
I use `question`. A rhetorical question used mainly to express an opinion does
not automatically become `question`.

---

## The Dataset

**Where the posts came from:**

The dataset consists of public text posts collected from the public Reddit
community **r/nba**.

The posts were collected from one community so the classifier learns
distinctions within a consistent discussion environment rather than mixing
different communities with different writing styles.

Only publicly visible content was used.

**How I labelled them:**

I first defined the taxonomy and decision rules before labeling the full
dataset.

The first 20 posts were labeled manually without AI assistance and marked as
`cold` in the `note` column.

For later posts, AI assistance may be used to suggest a preliminary label.
Every AI-suggested label is reviewed against my written taxonomy and corrected
by me before it is included in the final dataset.

The CSV uses exactly these columns:

```text
text,label,note
```

The file remains as one unsplit `labels.csv`. The notebook performs the
train/validation/test split later.

**Counts per label:**

> TODO: Replace this table after `labels.csv` is complete.

| Label | Count | Share |
|---|---:|---:|
| `analysis` | TODO | TODO |
| `hot_take` | TODO | TODO |
| `reaction` | TODO | TODO |
| `question` | TODO | TODO |
| **Total** | **TODO** | **100%** |

No label should represent more than 70% of the complete dataset.

### Three hard cases

These are examples where I had to apply the decision rules instead of relying
on an obvious keyword or tone.

**1. Analysis vs. hot take**

> **The post:**  
> TODO: Paste the real post.

> **Could have been:**  
> `analysis` or `hot_take`

> **I chose:**  
> TODO

> **Why:**  
> TODO: Explain whether the post actually supplied evidence that supported its
> claim.

---

**2. Reaction vs. hot take**

> **The post:**  
> TODO: Paste the real post.

> **Could have been:**  
> `reaction` or `hot_take`

> **I chose:**  
> TODO

> **Why:**  
> TODO: Explain whether its main purpose was reacting to a recent event or
> making a broader unsupported claim.

---

**3. Question vs. hot take/analysis**

> **The post:**  
> TODO: Paste the real post.

> **Could have been:**  
> `question` or another label

> **I chose:**  
> TODO

> **Why:**  
> TODO: Explain whether the post genuinely requested information/opinion or was
> mainly making its own argument.

---

## The Training Run

**Base model:**

```text
distilbert-base-uncased
```

The model is fine-tuned locally using the TakeMeter training notebook.

**Training device:**

```text
mps — Apple Silicon GPU (MPS)
```

My environment check reported:

```text
PyTorch 2.14.1
Apple Silicon GPU (MPS)
```

### Initial settings

```text
Epochs:        3
Learning rate: 2e-5
Batch size:    16
Maximum length: 128 tokens
Seed:          42
Split:         70% train / 15% validation / 15% test
```

These are the starter notebook defaults.

**Anything I changed from the defaults, and why:**

For the initial training run, I plan to keep the default settings so that the
first result provides a clean baseline for comparison.

For the declared stretch second training run, I will change exactly one
hyperparameter and record both the original and new result.

### Split sizes

TODO: Paste the actual notebook output after Section 3 runs.

```text
train: TODO
validation: TODO
test: TODO
```

### Test-set counts

| Label | Test examples |
|---|---:|
| `analysis` | TODO |
| `hot_take` | TODO |
| `reaction` | TODO |
| `question` | TODO |

If any label has fewer than approximately eight examples in the test split, I
will record that because its measured F1 may vary substantially between random
splits.

### Training result

TODO after notebook Section 5 completes.

The notebook will create:

```text
results.json
test_split.csv
```

Both files will be committed to this repository.

---

## How I Used AI

### Moment 1 — Designing and stress-testing the taxonomy

**What I asked for:**

I used an AI assistant to help me examine whether my proposed categories for
r/nba discussions had clear, testable boundaries.

I specifically asked it to challenge distinctions such as:

```text
analysis vs. hot_take
reaction vs. hot_take
question vs. opinion
```

**What came back:**

The AI identified cases where emotional language alone would not be enough to
distinguish labels and suggested focusing on the post's purpose and whether its
claim had supporting evidence.

**What I changed:**

I kept the labels but made the decision rules more explicit.

For example, `analysis` now requires evidence that actually supports the claim,
while a confident unsupported conclusion remains `hot_take`.

I also defined `reaction` based on the post's primary purpose rather than simply
looking for emotional words.

---

### Moment 2 — Project setup and training workflow

**What I asked for:**

I used an AI assistant while setting up the TakeMeter repository, Python
environment, Jupyter notebook, and local training workflow.

**What came back:**

The assistant helped me verify the project environment, run `test.py`, identify
the Apple Silicon `mps` training device, and understand which notebook sections
belong to Unit 5.

**What I changed:**

I kept the starter training pipeline and default hyperparameters for the first
run rather than changing the training configuration before having a baseline.

The environment check completed successfully with:

```text
9 passed
0 failed
```

---

### Pre-labelling disclosure

The first 20 examples are labeled manually without AI assistance and recorded
as cold labels.

For later examples, I may use AI to suggest preliminary labels in batches.
Those suggestions are not accepted automatically. I review each post against
the written taxonomy and decision rules and correct the label myself when
necessary.

Rows that received AI-assisted preliminary labeling are identified in the
`note` column.

---

# Unit 6 — Test

The sections below are intentionally left for Unit 6. They should be completed
only after the Unit 5 model and dataset are finished.

---

## Baseline vs. Trained

| Measure | Baseline | Trained | Difference |
|---|---:|---:|---:|
| Overall accuracy | TODO | TODO | TODO |
| Macro F1 | TODO | TODO | TODO |
| F1 — `analysis` | TODO | TODO | TODO |
| F1 — `hot_take` | TODO | TODO | TODO |
| F1 — `reaction` | TODO | TODO | TODO |
| F1 — `question` | TODO | TODO | TODO |

**What I predicted before I looked:**

TODO in Unit 6 before viewing the comparison.

**What the gap actually means:**

TODO in Unit 6.

---

## Run Log — Before

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. TODO | TODO | TODO | TODO | TODO | TODO |
| 2. TODO | TODO | TODO | TODO | TODO | TODO |
| 3. TODO | TODO | TODO | TODO | TODO | TODO |
| 4. TODO | TODO | TODO | TODO | TODO | TODO |
| 5. TODO | TODO | TODO | TODO | TODO | TODO |

### Confusion matrix

TODO: Paste the notebook's markdown confusion matrix here as text in Unit 6.

| true \ predicted | analysis | hot_take | reaction | question |
|---|---:|---:|---:|---:|
| **analysis** | TODO | TODO | TODO | TODO |
| **hot_take** | TODO | TODO | TODO | TODO |
| **reaction** | TODO | TODO | TODO | TODO |
| **question** | TODO | TODO | TODO | TODO |

**My biggest off-diagonal number, and what it means:**

TODO in Unit 6.

---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | TODO | TODO | TODO | TODO |
| 2 | TODO | TODO | TODO | TODO |
| 3 | TODO | TODO | TODO | TODO |
| 4 | TODO | TODO | TODO | TODO |
| 5 | TODO | TODO | TODO | TODO |

### Diagnoses

TODO in Unit 6.

Each missed criterion will be diagnosed using the model results, confusion
matrix, dataset distribution, and agreement report rather than changing a
target simply because the model missed it.

---

## Agreement Report

**Agreement rate:**

```text
TODO / 30 = TODO%
```

### Disagreements

**1.**

> **The post:**  
> TODO

> **Staff said / I said:**  
> TODO

> **My call, and why:**  
> TODO

> **Which pile:**  
> TODO

---

**2.**

> **The post:**  
> TODO

> **Staff said / I said:**  
> TODO

> **My call, and why:**  
> TODO

> **Which pile:**  
> TODO

---

**What the pattern in my disagreements tells me:**

TODO in Unit 6.

---

## The Improvement

**What I changed:**

TODO in Unit 6 after diagnosis.

**Which diagnosis pointed at it:**

TODO.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. TODO | TODO | TODO | TODO | TODO | TODO |
| 2. TODO | TODO | TODO | TODO | TODO | TODO |
| 3. TODO | TODO | TODO | TODO | TODO | TODO |
| 4. TODO | TODO | TODO | TODO | TODO | TODO |
| 5. TODO | TODO | TODO | TODO | TODO | TODO |

**Did it help, and how do I know:**

TODO in Unit 6.

---

## What's Still Broken

TODO in Unit 6.

For every criterion that remains missed, I will describe what I would try next
and why I stopped after the measured improvement rather than continuing to
change the system without evaluation.

**The gap between what I meant my labels to capture and what the model
learned:**

TODO after examining the confusion matrix and agreement results.

---

# Unit 5 Submission Checklist

```text
[ ] criteria.md has five numbered criteria
[ ] every criterion includes a numeric target
[ ] every criterion includes a reason
[ ] labels.csv contains at least 200 examples
[ ] labels.csv has text, label, and note columns
[ ] first 20 posts are cold/manual labels
[ ] no label is above 70%
[ ] Label Taxonomy has two real examples per label
[ ] decision rule for hardest boundary is documented
[ ] three hard cases are documented
[ ] notebook Sections 1–5 completed
[ ] results.json committed
[ ] test_split.csv committed
[ ] README Unit 5 sections completed
[ ] at least four new commits
[ ] repository pushed to GitHub
```

---

# Stretch Tracking

### Fourth label

Declared before build:

```text
question
```

The reason for this fourth label is that some community posts primarily request
information or opinions rather than making an argument, unsupported claim, or
immediate reaction.

TODO: Confirm with real collected posts that this is a genuine recurring
category in the dataset.

### Second training run

Declared before build.

First run:

```text
epochs: 3
learning rate: 2e-5
batch size: 16
max length: 128
```

Second run:

```text
TODO: Change exactly one hyperparameter.
```

**What moved:**

TODO after the second training run.

### 50 additional examples for the thinnest label

Declared before build.

**Thinnest label after the initial dataset:**

```text
TODO
```

**Initial count:**

```text
TODO
```

**Additional examples added:**

```text
50+
```

**Where they came from:**

```text
r/nba
```

**What happened to that label's result:**

TODO after training/evaluation.

---

## Running the Project

See:

[RUNNING.md](RUNNING.md)