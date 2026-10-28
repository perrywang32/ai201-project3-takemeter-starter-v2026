# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

This project classifies Clippers basketball discussion posts from Reddit and X into `analysis`, `hot_take`, and `reaction`. The current dataset contains 183 labeled posts, below the 200-post target in the brief. The notebook trained and evaluated a DistilBERT classifier on a stratified split.

---

## Label Taxonomy

### `analysis`

**Definition:** Makes an argument supported by a specific, checkable fact that does real work in the argument.

**Example 1:**
> “Median finish was 3:58 last year”

**Example 2:**
> “capped long runs at 2 hours”

### `hot_take`

**Definition:** Makes a confident claim without offering support for it.

**Example 1:**
> “Super shoes have ruined amateur racing”

**Example 2:**
> “Anyone running more than 60 miles”

### `reaction`

**Definition:** Gives an immediate personal response to something that just happened.

**Example 1:**
> “I cannot feel my hands”

**Example 2:**
> “I'm genuinely shaking”

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The decision rule I used every time:** A checkable fact only makes a post `analysis` when it supports the argument. If the fact is just decoration for an unsupported conclusion, the post is `hot_take`.

---

## The Dataset

**Where the posts came from:** A mix of Reddit and X/Twitter comments about the Clippers, transcribed from screenshots.

**How I labelled them:** AI suggestions were used while assembling labels; I need to verify the labels against the definitions and correct any mistakes. The CSV notes mark these rows for verification.

**Counts per label:**

| Label | Count | Share |
|---|---:|---:|
| analysis | 61 | 33.3% |
| hot_take | 67 | 36.6% |
| reaction | 55 | 30.1% |
| **Total** | **183** | **100%** |

**Three hard cases:** I still need to choose and explain three borderline examples from the dataset.

---

## The Training Run

**Base model:** `distilbert-base-uncased`.

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, maximum length 128. The main run used seed 42; the stability check used seeds 42, 7, and 2024.

**Anything I changed from the defaults, and why:** I kept the notebook's training defaults.

**Hardware:** CPU; PyTorch `2.14.1+cpu`.

**Split sizes:** 127 train / 28 validation / 28 test. The seed-42 test split had 9 `analysis`, 10 `hot_take`, and 9 `reaction` posts.

The dataset check found 183 rows, 17 below the 200-post target in the brief. Results below are from this dataset and should be interpreted cautiously, especially given the seed-to-seed spread.

---

## How I Used AI

**Moment 1**

- *What I asked for:* Help transcribing and labeling a mix of Reddit and X/Twitter screenshot comments for the project.
- *What came back:* Draft post text and suggested labels.
- *What I changed:* I assembled the suggestions into `labels.csv`. The labels still need a careful human audit against the written definitions; the notes flag them for verification.

**Moment 2**

- *What I asked for:* Help running the notebook and interpreting its evaluation output.
- *What came back:* Guidance on loading `labels.csv`, running the notebook, and reading its metrics.
- *What I changed:* I ran the notebook on the 183-post dataset and am using its saved results as the evidence reported below.

**Pre-labelling disclosure:** AI suggestions were used to draft labels. I have not yet completed a full manual verification pass, so the current scores may reflect labeling errors as well as model limitations.

---

## Baseline vs. Trained

Both models were evaluated on the same 28 held-out posts from `test_split.csv`. The zero-shot baseline used the label definitions in `label_definitions.txt`.

| Measure | Baseline | Trained | Difference |
|---|---:|---:|---:|
| Overall accuracy | 0.500 | 0.607 | +0.107 |
| Macro F1 | 0.399 | 0.535 | +0.135 |
| F1 — `analysis` | 0.643 | 0.737 | +0.094 |
| F1 — `hot_take` | 0.000 | 0.667 | +0.667 |
| F1 — `reaction` | 0.556 | 0.200 | -0.356 |

**Prediction made before running the baseline:** I expected the trained model to do better overall, especially on `analysis` and `hot_take`, while the baseline might do as well or better on `reaction`. I had already seen the trained model's results, so this was a prediction about the comparison, not a blind prediction of the trained scores.

**What the gap means:** The prediction was borne out on overall accuracy and macro F1: fine-tuning improved them by 0.107 and 0.135. It also improved `analysis` and especially `hot_take`, but the baseline did much better on `reaction`. This suggests the fine-tuned model's main weakness is recognizing `reaction`; it does not show that fine-tuning helped every label.
---

## Run Log — Before

The criteria in `criteria.md` were written after the initial run, so these targets are retrospective rather than pre-run acceptance criteria.

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---:|---:|---:|---:|
| Overall accuracy | 0.607 | 0.500 | 0.607 | 0.107 |
| Macro F1 | 0.535 | 0.402 | 0.586 | 0.184 |
| F1 — `analysis` | 0.737 | 0.727 | 0.889 | 0.162 |
| F1 — `hot_take` | 0.667 | 0.480 | 0.583 | 0.187 |
| F1 — `reaction` | 0.200 | 0.000 | 0.286 | 0.286 |

### Confusion matrix — seed 42

| true \ predicted | analysis | hot_take | reaction |
|---|---:|---:|---:|
| **analysis** | 7 | 2 | 0 |
| **hot_take** | 1 | 9 | 0 |
| **reaction** | 2 | 6 | 1 |

**Biggest off-diagonal number:** Six true `reaction` posts were predicted as `hot_take`, while no `hot_take` posts were predicted as `reaction`. The model is confusing immediate reactions with confident takes in this test split.

---

## Verdicts and Diagnoses

Using the targets in `criteria.md`:

| # | Criterion | Target | Results | Verdict |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.60 each seed | 0.607, 0.500, 0.607 | MISSED |
| 2 | Macro F1 | ≥ 0.60 each seed | 0.535, 0.402, 0.586 | MISSED |
| 3 | Every label F1 | ≥ 0.50 each seed | `reaction` F1 was 0.200, 0.000, 0.286 | MISSED |
| 4 | Accuracy stability | spread ≤ 0.10 | spread 0.107 | MISSED |
| 5 | Confidence usefulness | most-sure third ≥ least-sure third + 0.10 | See notebook output; inspect each seed before finalizing verdict | PENDING |

**Diagnosis:** The seed-42 matrix shows a strong directional confusion: 6 of 9 actual `reaction` posts were called `hot_take`. Also, accuracy varies by 0.107 across seeds, exceeding the stability target. Because the labels have not yet had a full manual audit and the dataset is below the brief's 200-post target, I cannot yet separate model limitations from label ambiguity or data volume.

---

## Agreement Report

**Status:** Pending. I need to label the 30 staff posts myself under `data/staff_taxonomy.md` and obtain the matching staff labels file before calculating agreement. `my_staff_labels_template.csv` contains the exact post text and a blank label column.

---

## The Improvement

**What I changed:** Added eight reaction-labeled posts to the dataset to increase examples for the weakest class. This changed the dataset from 183 to 191 posts; it remains below the 200-post target.

**Which diagnosis pointed at it:** The earlier run showed very low `reaction` F1 and frequent confusion with `hot_take`; increasing reaction examples was the chosen data change.

### Run Log — After

The latest three-seed run used the 191-post dataset. The intervention was adding eight reaction examples; the initial and latest runs used different dataset sizes and test splits, so the change cannot be isolated as the sole cause of score differences.

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---:|---:|---:|---:|
| Overall accuracy | 0.655 | 0.517 | 0.552 | 0.138 |
| Macro F1 | 0.649 | 0.463 | 0.551 | 0.186 |
| F1 — `analysis` | 0.737 | 0.727 | 0.842 | 0.115 |
| F1 — `hot_take` | 0.640 | 0.480 | 0.476 | 0.164 |
| F1 — `reaction` | 0.571 | 0.182 | 0.333 | 0.390 |

### Confusion matrix — latest run, seed 42

| true \ predicted | analysis | hot_take | reaction |
|---|---:|---:|---:|
| **analysis** | 7 | 2 | 0 |
| **hot_take** | 2 | 8 | 0 |
| **reaction** | 1 | 5 | 4 |

**Biggest off-diagonal number:** Five true `reaction` posts were predicted as `hot_take`; there were no `hot_take` posts predicted as `reaction`. The confusion remains directional, though the latest seed-42 run correctly identified 4 of 10 reactions.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.60 each seed | 0.655 | 0.517 | 0.552 | MISSED |
| 2. Macro F1 | ≥ 0.60 each seed | 0.649 | 0.463 | 0.551 | MISSED |
| 3. Every label F1 | ≥ 0.50 each seed | `reaction`: 0.571, 0.182, 0.333 | — | — | MISSED |
| 4. Accuracy stability | spread ≤ 0.10 | spread 0.138 | — | — | MISSED |
| 5. Confidence usefulness | most-sure third ≥ least-sure third + 0.10 | 0.889 vs 0.556 on seed 42; other seeds not reported | — | — | PENDING |

**Did it help, and how do I know:** Seed-42 `reaction` F1 rose from 0.200 in the initial run to 0.571 in the latest run, and the latest confusion matrix correctly identifies 4 of 10 reactions. The dataset and test split changed, so this is encouraging but does not isolate the added examples as the cause.

---

## What's Still Broken

- The dataset has 191 posts, 9 below the 200-post target; labels are a mix of Reddit and X/Twitter comments.
- AI-assisted draft labels still need a full manual audit against the label definitions.
- The baseline comparison is complete; the staff agreement check is still pending.
- In the latest 191-post run, accuracy spread across the three seeds is 0.138, above the 0.10 stability target.
- The confidence usefulness verdict still needs to be checked against each seed's detailed output.

**The gap between the label definitions and what the model learned:** On the latest seed-42 run, 5 actual `reaction` posts were classified as `hot_take`, while none of the 10 actual `hot_take` posts was classified as `reaction`. The model appears to rely on language shared by emphatic reactions and opinionated takes; a manual label audit and staff agreement results are needed before deciding whether this is mostly a model or labeling issue.

---

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
