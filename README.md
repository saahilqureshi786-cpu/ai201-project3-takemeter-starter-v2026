# TakeMeter

TakeMeter is a CodePath AI201 text classifier for public **r/nba** posts. It fine-tunes `distilbert-base-uncased` in `takemeter.ipynb` to classify posts as `analysis`, `hot_take`, `reaction`, or `question`.

## What This Does

The classifier identifies the main purpose of an r/nba post: supported basketball reasoning, an unsupported take, an immediate reaction, or a question to the community.

## Label Taxonomy

### `analysis`
A claim supported by a specific statistic, checkable fact, comparison, example, or concrete reasoning.

Examples:
1. “Is Lebron James a bigger global star than Virat Kohli? Virat Kohli: 271 Million Instagram followers. Lebron James: 153 Million Instagram followers.”
2. “Ben Simmons good again? ... he showed out in the preseason ... shooting mid ranges ... showing off his playmaking ability.”

### `hot_take`
A confident opinion, prediction, ranking, or conclusion without meaningful supporting evidence.

Examples:
1. “Why the Spurs run wasn’t legit and Minnesota is the better team”
2. “Nikola Jokic is the greatest passer of all time.”

### `reaction`
A post mainly reacting to a game, player performance, trade, highlight, injury, announcement, or other recent event.

Examples:
1. “Ben Simmons ☠️🚫🐟”
2. “With Ben Simmons now out with a torn Achilles injury ... Horrible to see, especially as the season hasn't even started.”

### `question`
A post mainly asking the community for an explanation, comparison, prediction, recommendation, or opinion.

Examples:
1. “[OC] Which team does every fanbase hate the most?”
2. “What more popular worldwide: Basketball or Cricket?”

### Hardest boundary

The hardest boundary is `analysis` vs. `hot_take`.

**Decision rule:** if a claim has at least one specific, checkable fact, statistic, concrete comparison, or clear piece of reasoning that actually supports the conclusion, I label it `analysis`. If it mainly asserts a conclusion without meaningful support, I label it `hot_take`.

For `reaction` vs. `hot_take`, I use `reaction` when the main purpose is responding to something that just happened. For `question`, the main purpose must genuinely be asking the community something rather than using a rhetorical question to make a claim.

## The Dataset

**Source:** public posts from **r/nba**. I collected a 300-post working pool from a public Reddit archive and built `labels.csv` from 200 unique reviewed posts.

The first 20 posts were labeled manually without AI and marked `cold`. For later posts, AI suggested preliminary labels; I read every post and accepted or corrected the suggestion. The `note` column records cold, AI-reviewed, or AI-corrected rows.

| Label | Count | Share |
|---|---:|---:|
| `analysis` | 37 | 18.5% |
| `hot_take` | 24 | 12.0% |
| `reaction` | 87 | 43.5% |
| `question` | 52 | 26.0% |
| **Total** | **200** | **100%** |

No label exceeds the assignment's 70% cap. My stricter acceptance target was 40%; `reaction` reached 43.5%, so that criterion is a miss and I did not change the target afterward.

### Three hard cases

1. **`analysis` vs. `question`:** “Ben Simmons good again? ... he showed out in the preseason ... shooting mid ranges ...” I chose `analysis` because the body argues for a conclusion using concrete observations even though the title is a question.
2. **`question` vs. `analysis`:** “Lakers are bound to lose Luka? ... What do you guys think?” I chose `question` because the stated main purpose is asking the community to evaluate the possibility.
3. **`reaction` vs. `analysis`:** “With Ben Simmons now out with a torn Achilles injury ... Horrible to see ...” I chose `reaction` because the main purpose is emotional response rather than an evidence-backed argument.

## The Training Run

Starting model: `distilbert-base-uncased`

Settings:
- epochs: 3
- learning rate: `2e-5`
- batch size: 16
- max length: 128
- seed: 42
- device: Apple Silicon GPU (`mps`)
- split: 139 train / 31 validation / 30 test

Results:
- accuracy: **0.433**
- macro F1: **0.151**
- `analysis` F1: **0.000** (n=5)
- `hot_take` F1: **0.000** (n=4)
- `reaction` F1: **0.605** (n=13)
- `question` F1: **0.000** (n=8)
- most-confident third accuracy: **0.900**
- least-confident third accuracy: **0.000**

The model learned `reaction` much more strongly than the other labels. I report the result as-is rather than changing the acceptance criteria after training. The notebook wrote `results.json` and `test_split.csv`.

## How I Used AI

**Moment 1 — taxonomy:** I used an AI assistant to stress-test the label boundaries, especially `analysis` vs. `hot_take`, `reaction` vs. `hot_take`, and genuine questions vs. rhetorical questions. I used that feedback to make the written rules more concrete.

**Moment 2 — workflow/debugging:** I used AI for environment setup, Jupyter debugging, data collection workflow, and a review tool. AI also suggested preliminary labels after the first 20 cold examples. I reviewed each suggestion before it entered the final dataset.

## Stretch Status

I declared the stretch work before implementation. The genuine fourth label, `question`, is completed and appears in 52/200 posts (26%). The second hyperparameter run and 50 extra examples for the thinnest label were declared but not completed for Unit 5.

## Unit 5 Files

`criteria.md`, `labels.csv`, `results.json`, `test_split.csv`, `label_definitions.txt`, `takemeter.ipynb`, `README.md`

Setup instructions are in `RUNNING.md`.

---

# Unit 6 — Testing

## Unit 6 Testing Plan and Stretch Features — Declared Before Testing

### Baseline prediction

Before running the zero-shot baseline, I predicted that the baseline might outperform my fine-tuned model on `analysis`, `hot_take`, and `question`, because the Unit 5 model had learned `reaction` much more strongly than the other labels. I expected the trained model's strongest relative advantage to remain on `reaction`.

### Stretch features declared before testing

1. **Confidence check:** compare the model's most-confident predictions with its least-confident predictions.
2. **Per-label error write-up:** analyze the worst-performing label and its most common confusion.
3. **Second measured improvement:** make a second diagnosis-driven training change, rerun the same three seeds, and report whether it helped.

## Baseline vs. Trained

Both models were evaluated on the same 30 posts from `test_split.csv`.

| Measure | Baseline | Trained | Difference |
|---|---:|---:|---:|
| Overall accuracy | 0.233 | 0.433 | +0.200 |
| Macro F1 | 0.160 | 0.151 | -0.009 |
| F1 — `analysis` | 0.333 | 0.000 | -0.333 |
| F1 — `hot_take` | 0.000 | 0.000 | +0.000 |
| F1 — `question` | 0.308 | 0.000 | -0.308 |
| F1 — `reaction` | 0.000 | 0.605 | +0.605 |

The fine-tuned model improved overall accuracy by 20 percentage points and learned `reaction` much better than the zero-shot baseline. The baseline was still better on `analysis` and `question`, showing that the first trained model specialized too heavily toward the dominant `reaction` class.

---

## Run Log — Before

Hardware for all three trials: **Apple Silicon GPU (MPS)**, PyTorch **2.14.1**.

Training settings: **3 epochs**, learning rate **2e-5**, batch size **16**, maximum length **128**.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.70 | 0.433 | 0.500 | 0.500 | **MISSED** |
| 2. Macro F1 | ≥ 0.65 | 0.151 | 0.253 | 0.267 | **MISSED** |
| 3. Every label F1 | ≥ 0.55 | min 0.000 | min 0.000 | min 0.000 | **MISSED** |
| 4. Largest dataset label share | ≤ 40% | 43.5% | 43.5% | 43.5% | **MISSED** |
| 5. Human agreement | ≥ 80% | 40% | 40% | 40% | **MISSED** |

Accuracy spread across the three seeds was **0.067**, while macro-F1 spread was **0.116**. The accuracy result was fairly stable, but the per-label behavior was not: `question` moved from 0.000 F1 on seed 42 to 0.400 on seed 2024.

### Per-label results — before

| Label | Seed 42 F1 | Seed 7 F1 | Seed 2024 F1 |
|---|---:|---:|---:|
| `analysis` | 0.000 | 0.000 | 0.000 |
| `hot_take` | 0.000 | 0.000 | 0.000 |
| `reaction` | 0.605 | 0.650 | 0.667 |
| `question` | 0.000 | 0.364 | 0.400 |

### Confusion matrix — before, seed 42

| true \ predicted | analysis | hot_take | reaction | question |
|---|---:|---:|---:|---:|
| **analysis** | 0 | 0 | 5 | 0 |
| **hot_take** | 0 | 0 | 4 | 0 |
| **reaction** | 0 | 0 | 13 | 0 |
| **question** | 0 | 0 | 8 | 0 |

The biggest single confusion was **8 real `question` posts predicted as `reaction`**, with zero errors in the reverse direction. On this seed the model collapsed almost completely to `reaction`.

---

## Verdicts and Diagnoses

### Criterion 1 — Overall accuracy ≥ 70%: MISSED before

The three accuracies were 0.433, 0.500, and 0.500. The seed-42 confusion matrix shows the main cause: the model predicted every test item as `reaction`. This made the common class look reasonable while failing the other distinctions.

### Criterion 2 — Macro F1 ≥ 0.65: MISSED

Macro F1 was only 0.151, 0.253, and 0.267 before the fix. Accuracy hid how weak the minority labels were: `analysis` and `hot_take` were 0.000 F1 on every seed, and `question` was 0.000 on seed 42.

### Criterion 3 — Every label F1 ≥ 0.55: MISSED

The failure was concentrated in the smaller and harder labels. `hot_take` had only **24 examples (12%)**, compared with **87 `reaction` examples (43.5%)**. The agreement exercise also showed that I did not consistently distinguish unsupported general claims, evidence-backed arguments, and immediate reactions. That combination of thin data and inconsistent boundaries is stronger evidence than simply saying the model "did badly."

### Criterion 4 — No label over 40% of the dataset: MISSED

`reaction` was **87/200 = 43.5%**, above my pre-registered 40% target. I did not lower the target after seeing the data. The initial confusion matrix is consistent with this imbalance: the model leaned heavily toward the largest class.

### Criterion 5 — Human agreement ≥ 80%: MISSED

I agreed with staff on **12 of 30 posts = 40%**, with 18 disagreements. This was not a model score; it was evidence about my own consistency in applying a written taxonomy. Most disagreements came from treating a number as automatically making a post `analysis`, or from missing the difference between an unsupported general claim (`hot_take`) and an immediate personal experience (`reaction`).

---

## Agreement Report

I labeled all 30 staff posts before receiving the staff key. The comparison was:

- compared: **30**
- agreed: **12**
- disagreed: **18**
- agreement rate: **40%**

For each disagreement below, I applied the staff rules again and made a specific call.

| Post excerpt | Staff | Mine | My adjudication | Pile |
|---|---|---|---|---|
| "I tracked sleep... eleven weeks... 6-8 seconds per mile slower..." | `analysis` | `hot_take` | **Staff.** The eleven-week comparison and 6–8 second effect are load-bearing evidence for the scheduling conclusion. | (a) |
| "Scoring changed in 2019 from top-6 to top-10... B team... twice." | `analysis` | `reaction` | **Staff.** The rule change and two observed outcomes directly support the claim about turnout. | (a) |
| "Carbon plates... times dropped 2%... another 2%... 35 to 50 miles." | `analysis` | `hot_take` | **Staff.** The two 2% changes and mileage change are the argument, not decoration. | (a) |
| "41%... never run the distance... against 26% five years ago." | `analysis` | `reaction` | **Staff.** The 41% vs 26% comparison is used to explain the changed finish-line photos. | (a) |
| "Results going back to 2012... between 18 and 23 percent." | `analysis` | `hot_take` | **Staff.** The multi-year range is explicit evidence for the base-rate conclusion. | (a) |
| "10K route... remeasured in 2021... 60 metres long... about 12 seconds." | `analysis` | `reaction` | **Staff.** The measurement and 12-second consequence carry the argument about incomparable PBs. | (a) |
| "Heart rate zones are astrology for runners." | `hot_take` | `reaction` | **Staff.** It is a general unsupported claim about running, not something that just happened to the writer. | (a) |
| "The club needs to stop pretending the Tuesday session is for everyone." | `hot_take` | `analysis` | **Staff.** No checkable evidence is offered; it is a confident general claim. | (a) |
| "Marathon training plans are all the same plan with different fonts." | `hot_take` | `reaction` | **Staff.** It asserts a general view and contains no immediate personal event. | (a) |
| "If you need a gel before 90 minutes..." | `hot_take` | `analysis` | **Staff, narrowly.** The 90-minute threshold made my original `analysis` reading plausible, but the number is part of the unsupported claim rather than evidence for it. | (c) |
| "Track sessions are wasted on anyone slower than 8 minute miles." | `hot_take` | `reaction` | **Staff.** This is a general prescription with no immediate writer experience. | (a) |
| "Nobody actually enjoys the long run..." | `hot_take` | `analysis` | **Staff.** It is a broad unsupported claim; no fact or comparison is supplied as evidence. | (a) |
| "Got up at 5, drove ninety minutes, and it's cancelled." | `reaction` | `analysis` | **Staff.** The numbers are details of an immediate personal event, not evidence supporting an argument. | (a) |
| "First run back after eight weeks out... Two miles..." | `reaction` | `hot_take` | **Staff.** The post is about the writer's just-completed run and how it felt. | (a) |
| "Blew up at 20 with the pace band... Rough one." | `reaction` | `analysis` | **Staff.** Mile 20 describes the event; there is no argument being supported. | (a) |
| "Someone handed my kid an orange slice at mile 22... crying..." | `reaction` | `hot_take` | **Staff.** It is an immediate personal emotional response, not a claim about the world. | (a) |
| "Wind... leaning at about 20 degrees. Coming back felt like cheating." | `reaction` | `analysis` | **Staff, narrowly.** The "20 degrees" detail gave my `analysis` reading a hook, but the post never makes an argument; it reports a just-lived experience. | (c) |
| "Third DNF this year... just wanted to put it somewhere." | `reaction` | `hot_take` | **Staff.** It reports the writer's immediate state after another DNF rather than asserting a general claim. | (a) |

The agreement result changed my diagnosis: some classification difficulty is not just model capacity. I also need more consistent application of the written boundaries.

---

## Stretch — Confidence Check

The model's confidence was informative on the original three-seed run.

| Seed | Most-confident third accuracy | Least-confident third accuracy |
|---|---:|---:|
| 42 | 0.900 | 0.000 |
| 7 | 0.700 | 0.300 |
| 2024 | 0.900 | 0.300 |
| **Average** | **0.833** | **0.200** |

On all three seeds, the most-confident third was more accurate than the least-confident third. The model was weak as a complete classifier, but confidence still carried useful information about which predictions were more likely to be correct.

---

## Stretch — Per-label Error Write-up

The clearest persistent failure was **`hot_take`**.

Before any improvement, `hot_take` F1 was **0.000 on all three seeds**. In the confusion matrices, hot takes were usually sent to `reaction` and occasionally to `question`. This makes sense given the data: `hot_take` was the smallest class at **24/200**, while `reaction` was the largest at **87/200**. Both can use strong or emotional language, so short unsupported opinions can look superficially similar to reactions.

The agreement check gives a second piece of evidence. I repeatedly confused unsupported general claims with either reactions or analysis. Even after stronger training made `analysis` learnable, `hot_take` remained at 0.000 F1 on every bonus seed. That points to a data/boundary problem that a simple optimizer change did not solve.

---

## The Improvement — 3 epochs to 5 epochs

### Why this change

The initial model collapsed toward `reaction`, and three epochs left three labels poorly learned. I changed **one training setting only**: epochs from **3 to 5**. Learning rate stayed at **2e-5**.

### Run Log — After required improvement

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.70 | 0.600 | 0.567 | 0.667 | **MISSED** |
| 2. Macro F1 | ≥ 0.65 | 0.338 | 0.319 | 0.389 | **MISSED** |
| 3. Every label F1 | ≥ 0.55 | min 0.000 | min 0.000 | min 0.000 | **MISSED** |
| 4. Largest dataset label share | ≤ 40% | 43.5% | 43.5% | 43.5% | **MISSED** |
| 5. Human agreement | ≥ 80% | 40% | 40% | 40% | **MISSED** |

### Per-label F1 after 5 epochs

| Label | Seed 42 | Seed 7 | Seed 2024 |
|---|---:|---:|---:|
| `analysis` | 0.000 | 0.000 | 0.000 |
| `hot_take` | 0.000 | 0.000 | 0.000 |
| `reaction` | 0.765 | 0.774 | 0.828 |
| `question` | 0.588 | 0.500 | 0.727 |

Average accuracy increased from **0.478 to 0.611**, and average macro F1 increased from **0.224 to 0.348**. The change helped substantially, especially on `question`, but it still did not teach `analysis` or `hot_take`.

---

## Stretch — Second Measured Improvement

### Change

I kept **5 epochs** and changed one additional training setting: learning rate from **2e-5 to 5e-5**. The diagnosis was that longer training had helped but the model was still not moving enough to learn `analysis` or `hot_take`.

The third run is saved in `results_three_seeds_bonus.json`.

### Run Log — Bonus improvement

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.70 | 0.733 | 0.700 | 0.700 | **MET** |
| 2. Macro F1 | ≥ 0.65 | 0.547 | 0.539 | 0.479 | **MISSED** |
| 3. Every label F1 | ≥ 0.55 | min 0.000 | min 0.000 | min 0.000 | **MISSED** |
| 4. Largest dataset label share | ≤ 40% | 43.5% | 43.5% | 43.5% | **MISSED** |
| 5. Human agreement | ≥ 80% | 40% | 40% | 40% | **MISSED** |

### Per-label F1 — bonus run

| Label | Seed 42 | Seed 7 | Seed 2024 |
|---|---:|---:|---:|
| `analysis` | 0.571 | 0.545 | 0.333 |
| `hot_take` | 0.000 | 0.000 | 0.000 |
| `reaction` | 0.839 | 0.786 | 0.857 |
| `question` | 0.778 | 0.824 | 0.727 |

Average accuracy improved again from **0.611 to 0.711**, and average macro F1 improved from **0.348 to 0.522**. This second change clearly helped: it made `analysis` learnable for the first time and pushed overall accuracy to the pre-registered target on all three seeds.

---

## What's Still Broken

Four of the five original criteria are still missed.

- **Macro F1:** still below 0.65 because one class remains completely unlearned.
- **Per-label F1:** `hot_take` remains 0.000 on every seed, and `analysis` is still below 0.55 on two of the three bonus seeds.
- **Dataset balance:** `reaction` remains 43.5%, above my 40% target.
- **Human agreement:** the staff-set agreement remains 40%, showing that better model training does not fix inconsistent labeling boundaries.

The next change I would make is **data-focused, not another optimizer tweak**: collect and carefully review more genuine `hot_take` examples, then audit the `analysis` / `hot_take` / `reaction` boundary for consistency. I attempted to find 30 unused pre-labeled hot-take examples in the existing 300-post pool, but only seven unused candidates were available, so I did not silently add weak or fabricated examples just to hit a count.

The gap between the intended taxonomy and the learned behavior is now clear. I wanted four distinct discourse types; the first model mostly learned "reaction versus everything else." More training and a higher learning rate recovered `question` and part of `analysis`, but `hot_take` still lacks enough clean, consistent signal.

---

## How I Used AI — Unit 6

I used an AI assistant to help debug the environment and notebook workflow, compare the JSON results across seeds, and turn the measured error patterns into testable diagnoses. One important debugging moment was catching that my notebook file said 5 epochs while the running kernel still held the old value of 3; I reran the settings cell before accepting the improvement result.

AI also helped me organize the agreement disagreements against the written staff rules and identify which evidence supported each diagnosis. The baseline, three-seed metrics, confusion matrices, agreement rate, and confidence numbers all came from the project scripts and notebook outputs rather than being invented by the assistant.
