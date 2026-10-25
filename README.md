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

I declared the stretch work before implementation. The genuine fourth label, `question`, is completed and appears in 52/200 posts (26%). The second hyperparameter run and 50 extra examples for the thinnest label were declared but not completed for this submission.

## Unit 5 Files

`criteria.md`, `labels.csv`, `results.json`, `test_split.csv`, `label_definitions.txt`, `takemeter.ipynb`, `README.md`

Setup instructions are in `RUNNING.md`.

---

---

python - <<'PY'
from pathlib import Path
import subprocess

p = Path("README.md")
old = p.read_text(encoding="utf-8")
prefix = old.split("\n# Unit 6 — Testing")[0].rstrip()

unit6 = subprocess.check_output(["pbpaste"]).decode("utf-8").strip()

p.write_text(prefix + "\n\n---\n\n" + unit6 + "\n", encoding="utf-8")
print("README updated.")
PY

grep -n "Run Log — Before" README.md
git add README.md
git commit -m "Complete Unit 6 testing report and bonus analysis"
git push origin main
