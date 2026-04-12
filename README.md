# Controllable Latent Steering in VAEs

A notebook-first research repository on **controllable latent editing** in variational autoencoders, centered on one practical question:

> How far can we push a target attribute in latent space **without breaking the content we want to preserve**?

The main experimental path starts on **MNIST** with stroke-thickness steering, then extends to **dSprites** with an explicit **out-of-distribution (OOD)** split. The overall conclusion is consistent across the project:

- a **global direction** already works surprisingly well,
- **local directions** help, but not dramatically on their own,
- the strongest improvement comes from **safe per-sample step selection**,
- representation quality matters at least as much as the direction family.

---

## TL;DR

This repository studies **reliable controllable editing** rather than just visually plausible latent traversals.

The core update is

\[
z' = z + \alpha \, d(z),
\]

where `z` is a latent code, `d(z)` is a steering direction, and `α` is the step size.

The project compares three versions of this idea:

1. **Global steering** — one shared direction for all samples.
2. **Local steering** — cluster-conditioned or neighborhood-conditioned directions.
3. **Safe automatic steering** — choose `α*` per sample under preservation constraints.

The main finding is simple: **choosing the step safely matters more than making the direction local**.

---

## What is in this repository

This archive now contains **two related tracks**:

### 1) Main image-steering research track
The primary research path, developed week by week:

- **Week 5** — MNIST baseline VAE, classifier, and attribute probe
- **Week 6** — global latent steering on MNIST
- **Week 7** — local / clustered steering on MNIST
- **Week 8** — safe per-sample step selection `α*`
- **Week 9** — `β`-VAE ablation
- **Week 10** — dSprites transfer and OOD experiments
- **Week 11** — aggregation tables and comparison summaries
- **Week 12** — final paper material and independent-preservation notebook

### 2) Exploratory text-steering branch
An additional directory with a separate exploratory line:

- `steering-on-text-corpus/` — text detox steering notebook, paper files, and packaged outputs

So the repository is not only a single sequence of MNIST notebooks anymore; it also contains **later aggregation artifacts** and **a text-steering side branch**.

---

## Main takeaways

### MNIST

- A reasonably good latent representation is already enough to support targeted edits.
- A **global direction** increases thickness while keeping digit identity mostly stable for moderate edits.
- **Local steering** improves the tradeoff, but only modestly in this setup.
- The biggest reliability gain comes from **automatic per-sample step selection** rather than locality alone.
- In the tested sweep, increasing `β` too much hurts reconstruction and does **not** improve controllability overall.

### dSprites / OOD

- The steering pipeline transfers beyond MNIST to a more structured synthetic dataset.
- Fixed-step steering can give larger raw target gain, but it also increases drift and harms preservation.
- **Auto-steering** is much more stable and preserves identity-like factors better, including on OOD splits.

---

## Results snapshot

The repository contains several result summaries across weeks. The headline picture is:

| Stage | Setting | Headline result |
|---|---|---|
| Week 5 | MNIST baseline | digit classifier acc = **0.9909**, thickness probe acc = **0.9893** |
| Week 6 | Global steering | at `α = 1.0`: digit acc = **0.9671**, probe gain = **+2.6281** |
| Week 7 | Local steering | `local_k16`, `α = 1.0`: digit acc = **0.9673**, probe gain = **+2.7005** |
| Week 8 | Safe auto-step | best policy: digit acc = **0.9722**, consistency = **1.0000**, probe gain = **+2.9347** |
| Week 9 | `β`-VAE ablation | reconstruction digit acc drops from **0.9569** (`β=1`) to **0.8590** (`β=4`) |
| Week 10–11 | dSprites OOD | auto-steering preserves much better than fixed-step baselines in aggregated summaries |

For the dSprites comparison tables stored in `week11/`, the pattern is especially clear:

- **fixed** steering often produces higher raw target gain,
- **auto** steering gives much better preservation consistency,
- the global-vs-local gap is smaller than the fixed-vs-auto gap.

That is the main research message of the repository.

---

## Repository layout

```text
.
├── README.md
├── LICENSE
├── latent_steering_paper_v8.pdf
├── week5/
│   └── week5-genai.ipynb
├── week6/
│   ├── week6.ipynb
│   └── Week_6_Memo.md
├── week7/
│   └── week7.ipynb
├── week8/
│   └── week8.ipynb
├── week9/
│   └── week9.ipynb
├── week10/
│   ├── week10.ipynb
│   ├── week10_vae_tuned.ipynb
│   ├── week10_local_steering.ipynb
│   ├── memo_baseline.md
│   ├── memo_vae_tuned.md
│   ├── memo_with_local_steering.md
│   ├── output.zip
│   ├── output_vae_tuned.zip
│   ├── output_local_steering.zip
│   └── dsprites_ndarray_co1sh3sc6or40x32y32_64x64.npz
├── week11/
│   ├── clean_comparison_table.csv
│   ├── final_table_pretty.csv
│   ├── headline_rows.csv
│   ├── mean_std_summary.csv
│   ├── latent_steering_paper_v6.pdf
│   ├── latent_steering_paper_v7_fixed.pdf
│   ├── week10-local-steering.ipynb
│   ├── week10-vae-tuned4790739c79.ipynb
│   └── week11_ready_for_aggregation.zip
├── week12/
│   ├── latent_steering_paper_v8.pdf
│   └── week11_independent_preservation.ipynb
└── steering-on-text-corpus/
    ├── weekx-text-detox-steering (5).ipynb
    ├── text_detox_steering_paper.tex
    ├── text_detox_steering_paper.pdf
    ├── text_detox_paper_package.zip
    └── output (7).zip
```

---

## Recommended reading order

### If you want the full story quickly
1. Read **`latent_steering_paper_v8.pdf`** in the repository root.
2. Check **`week11/final_table_pretty.csv`** for aggregated dSprites comparisons.
3. Open **Week 8** and **Week 10** notebooks if you want the most important experimental logic.

### If you want the full experimental progression
1. `week5/week5-genai.ipynb`
2. `week6/week6.ipynb`
3. `week7/week7.ipynb`
4. `week8/week8.ipynb`
5. `week9/week9.ipynb`
6. `week10/week10.ipynb`
7. `week10/week10_vae_tuned.ipynb`
8. `week10/week10_local_steering.ipynb`
9. `week11/*.csv`
10. `week12/week11_independent_preservation.ipynb`

### If you only care about the text-steering branch
Go directly to:

- `steering-on-text-corpus/weekx-text-detox-steering (5).ipynb`
- `steering-on-text-corpus/text_detox_steering_paper.pdf`

---

## How the steering works

The common template is:

1. **Encode** an input into latent space.
2. **Estimate a steering direction** from paired examples, cluster structure, or local neighborhoods.
3. **Move in latent space** with either a fixed step `α` or an automatically selected `α*`.
4. **Decode** the edited latent.
5. **Evaluate both target gain and preservation**.

This last step is essential. The project does not treat “the image changed” as a success criterion.

---

## Evaluation logic

### MNIST metrics

- **Probe gain / margin** — did thickness increase?
- **Digit accuracy** — was digit identity preserved?
- **Digit consistency** — did the predicted digit stay unchanged after steering?
- **Feature drift** — how far did the edited sample move from the reconstruction?

### dSprites metrics

- **Target gain** — did the intended factor move in the desired direction?
- **Preservation accuracy** — was the preserved factor still correct?
- **Preservation consistency** — did the preserved attribute remain unchanged?
- **Drift / objective** — how expensive the edit was in reconstruction-like terms.

This is why the project is about **reliable controllability**, not just pretty latent traversals.

---

## Environment

This is a **notebook-first research repo**, not a packaged library. There is no single official `requirements.txt` in the root, so the simplest setup is manual.

### Core packages used across the image-steering notebooks

- `torch`
- `torchvision`
- `numpy`
- `pandas`
- `matplotlib`
- `scikit-learn`
- `scipy`
- `tqdm`
- `jupyter`

### Optional packages used in the text-steering branch

- `datasets`
- `transformers`
- `sentence-transformers`

### Minimal setup

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install torch torchvision numpy pandas matplotlib scikit-learn scipy tqdm notebook
```

For the text-steering branch, additionally install:

```bash
pip install datasets transformers sentence-transformers
```

---

## Data

### MNIST
MNIST is loaded through `torchvision` and downloads automatically in the relevant notebooks.

### dSprites
The dSprites experiments use the official `.npz` dataset file. In this archive, a copy is present at:

- `week10/dsprites_ndarray_co1sh3sc6or40x32y32_64x64.npz`

Some notebooks still expect a manually specified variable such as `dsprites_npz_path`, so you may need to edit that path locally before running them.

---

## Running the notebooks

After setting up the environment:

```bash
jupyter notebook
```

Then execute notebooks top-to-bottom.

A practical order is:

1. start from **Week 5**,
2. move to **Week 6–8** for the main MNIST steering story,
3. use **Week 9** for the `β`-VAE check,
4. use **Week 10–12** for dSprites transfer, aggregation, and final paper artifacts.

---

## Important artifacts

### Final research write-up
- `latent_steering_paper_v8.pdf`
- `week12/latent_steering_paper_v8.pdf`

### Weekly notes and summaries
- `week6/Week_6_Memo.md`
- `week10/memo_baseline.md`
- `week10/memo_vae_tuned.md`
- `week10/memo_with_local_steering.md`

### Aggregated result tables
- `week11/clean_comparison_table.csv`
- `week11/final_table_pretty.csv`
- `week11/headline_rows.csv`
- `week11/mean_std_summary.csv`

### Text-steering materials
- `steering-on-text-corpus/text_detox_steering_paper.pdf`
- `steering-on-text-corpus/text_detox_steering_paper.tex`
- `steering-on-text-corpus/weekx-text-detox-steering (5).ipynb`

---

## Current limitations

The repository is intentionally closer to a **research logbook** than to a polished benchmark package.

Main limitations:

- most work is organized as notebooks rather than scripts,
- some outputs are stored as zipped run artifacts,
- experiment interfaces are not yet fully standardized,
- controllability still depends strongly on representation quality and proxy metrics,
- the OOD setting is meaningful, but still controlled rather than fully real-world.

These are not hidden issues; they are part of the actual state of the project.

---

## Good next steps

Natural follow-up directions include:

- stronger preservation objectives,
- better semantic safety constraints,
- standardized experiment scripts instead of notebook-only pipelines,
- multi-seed reporting everywhere,
- richer datasets and harder OOD settings,
- cleaner packaging of the text-steering branch as a separate subproject.

---

## License

This repository is released under the **MIT License**. See `LICENSE` for details.
