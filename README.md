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

The zero-shot baseline has **not been run yet**. The trained results below use the seed-42 held-out test split.

| Measure | Baseline | Trained |
|---|---:|---:|
| Overall accuracy | pending | 0.607 |
| Macro F1 | pending | 0.535 |
| F1 — `analysis` | pending | 0.737 |
| F1 — `hot_take` | pending | 0.667 |
| F1 — `reaction` | pending | 0.200 |

**Prediction before seeing the comparison:** I expect the trained model to outperform the zero-shot baseline overall, especially on the analysis and hot_take labels, because it was fine-tuned on these posts. I expect the baseline may do similarly or better on reaction, since that label is less clearly separated in my training data.

**What the gap actually means:** Pending the baseline run.

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

**What I changed:** Pending the baseline and staff agreement diagnosis.

**Which diagnosis pointed at it:** Pending.

### Run Log — After

Not run yet. After choosing one evidence-based improvement, set `RUN_LABEL = "after"` in notebook Section 6 and rerun the three seeds.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.60 each seed | pending | pending | pending | pending |
| 2. Macro F1 | ≥ 0.60 each seed | pending | pending | pending | pending |
| 3. Every label F1 | ≥ 0.50 each seed | pending | pending | pending | pending |
| 4. Accuracy stability | spread ≤ 0.10 | pending | pending | pending | pending |
| 5. Confidence usefulness | most-sure third ≥ least-sure third + 0.10 | pending | pending | pending | pending |

**Did it help, and how do I know:** Pending the after run.

---

## What's Still Broken

- The dataset has 183 posts, 17 below the 200-post target; labels are a mix of Reddit and X/Twitter comments.
- AI-assisted draft labels still need a full manual audit against the label definitions.
- The baseline comparison and staff agreement check are still pending.
- Accuracy spread across the three seeds is 0.107, above the 0.10 stability target.
- The confidence usefulness verdict still needs to be checked against each seed's detailed output.

**The gap between the label definitions and what the model learned:** On seed 42, 6 actual `reaction` posts were classified as `hot_take`, while only 1 of 10 actual `hot_take` posts was classified as `reaction`. The model appears to rely on language shared by emphatic reactions and opinionated takes; a manual label audit and staff agreement results are needed before deciding whether this is mostly a model or labeling issue.

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
