# Controllable Latent Steering in VAEs

A notebook-first research project on **controllable latent editing** in variational autoencoders (VAEs), with a focus on one practical question:

> Can we increase a target attribute in latent space without destroying the content we want to preserve?

The project starts on **MNIST** with stroke-thickness control and then extends to **dSprites** with an explicit **out-of-distribution (OOD)** split. Across the full study, the main conclusion is consistent: **reliable steering depends more on safe step selection and representation quality than on local directions alone**.

---

## Project summary

The core steering rule is

\[
z' = z + \alpha\, d(z),
\]

where:

- `z` is the latent code of an input sample,
- `d(z)` is a steering direction,
- `α` is the edit magnitude.

The project compares three increasingly strong versions of this idea:

1. **Global steering** — one shared direction for all samples.
2. **Local steering** — cluster-specific or neighborhood-conditioned directions.
3. **Safe automatic steering** — choose `α*` per sample under preservation constraints.

The experiments are organized as a staged weekly research program:

- **Week 5** — baseline MNIST VAE + digit classifier + thickness probe
- **Week 6** — global latent steering on MNIST
- **Week 7** — clustered local directions on MNIST
- **Week 8** — safe per-sample step selection `α*`
- **Week 9** — focused `β`-VAE ablation
- **Week 10** — dSprites OOD transfer study

---

## Main findings

### MNIST

- A baseline VAE with latent dimension 16 is strong enough to support controlled edits.
- A **global direction** already increases thickness with only moderate digit-identity loss for small-to-medium `α`.
- **Local directions** help, but only **slightly** in this setup.
- The biggest improvement comes from **automatic per-sample step selection**, not from locality alone.
- A stronger KL penalty in the tested `β`-VAE sweep **did not improve** controllability overall.

### dSprites OOD

- The steering pipeline transfers beyond MNIST to a factorized synthetic dataset.
- Under held-out orientations, **auto-steering** preserves shape far better than aggressive fixed-step edits.
- OOD transfer is real, but still limited by representation quality and probe quality.

---

## Results snapshot

These are the headline numbers consolidated in the final report.

| Stage | Setting | Key result |
|---|---|---|
| Week 5 | MNIST baseline | digit classifier acc = **0.9909**, thickness probe acc = **0.9893** |
| Week 6 | Global steering | at `α = 1.0`: digit acc = **0.9671**, probe gain = **+2.6281** |
| Week 7 | Local steering | best small-budget tradeoff: `local_k16`, `α = 1.0`, digit acc = **0.9673**, probe gain = **+2.7005** |
| Week 8 | Safe auto-step | best policy: digit acc = **0.9722**, consistency = **1.0000**, probe gain = **+2.9347** |
| Week 9 | `β`-VAE ablation | reconstruction digit acc drops from **0.9569** (`β=1`) to **0.8590** (`β=4`) |
| Week 10 | dSprites OOD | tuned global auto-steering on OOD: shape acc = **0.6838**, consistency = **1.0000**, scale gain = **1.1926** |

The most important qualitative result is this:

- **fixed local directions > global**, but only marginally;
- **safe `α*` > fixed `α`**, by a clear margin on reliability.

---

## Repository structure

```text
.
├── README.md
├── LICENSE
├── latent_steering_paper_v6.pdf
├── week5/
│   ├── week5-genai.ipynb
│   └── output/...                # archived baseline run artifacts
├── week6/
│   ├── week6.ipynb
│   ├── Week_6_Memo.md
│   └── runs/...                  # archived global-steering outputs
├── week7/
│   ├── week7.ipynb
│   └── runs/.../memo_week7.md
├── week8/
│   ├── week8.ipynb
│   └── runs/.../memo_week8.md
├── week9/
│   ├── week9.ipynb
│   └── runs/.../memo_week9.md
└── week10/
    ├── week10.ipynb
    ├── week10_vae_tuned.ipynb
    ├── week10_local_steering.ipynb
    ├── memo_baseline.md
    ├── memo_vae_tuned.md
    └── memo_with_local_steering.md
```

This is a **research repository**, not a packaged library. The main artifacts are:

- notebooks,
- archived run outputs,
- short weekly memos,
- the consolidated final paper.

---

## What each week does

### Week 5 — Baseline representation on MNIST
Builds the starting point:

- convolutional VAE,
- digit classifier,
- thickness probe,
- latent-space sanity checks and reconstructions.

Notebook: `week5/week5-genai.ipynb`

### Week 6 — Global latent steering
Learns one global thickness direction from paired latent differences and evaluates the tradeoff between:

- target change (probe margin),
- digit preservation,
- visual plausibility.

Notebook: `week6/week6.ipynb`

### Week 7 — Local directions
Tests the piecewise-linearity hypothesis by replacing one global direction with cluster-specific local directions.

Notebook: `week7/week7.ipynb`

### Week 8 — Safe automatic step selection
Replaces a single fixed step with **per-sample discrete search** over candidate step sizes and chooses `α*` under preservation constraints.

Notebook: `week8/week8.ipynb`

### Week 9 — `β`-VAE comparison
Checks whether stronger latent regularization improves steering or just damages reconstruction.

Notebook: `week9/week9.ipynb`

### Week 10 — dSprites OOD extension
Moves from MNIST to a structured factor dataset with held-out orientations.

There are three week-10 variants:

- `week10/week10.ipynb` — baseline dSprites experiment,
- `week10/week10_vae_tuned.ipynb` — tuned global model,
- `week10/week10_local_steering.ipynb` — local steering on dSprites.

---

## Environment

The notebooks import the following Python packages:

- `torch`
- `torchvision`
- `numpy`
- `pandas`
- `matplotlib`
- `scikit-learn`
- `scipy`
- `tqdm`
- `jupyter`

A minimal setup looks like this:

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install torch torchvision numpy pandas matplotlib scikit-learn scipy tqdm notebook
```

---

## Data

### MNIST
MNIST is loaded through `torchvision` and downloads automatically in the baseline notebook.

### dSprites
The dSprites experiments expect the official `.npz` file to be available locally. In the week-10 notebooks, the path is controlled by `dsprites_npz_path`, so you may need to edit that value before running the notebook on your machine.

---

## How to reproduce the workflow

Because the project is notebook-first, the intended workflow is straightforward:

1. Start with **Week 5** to train or inspect the baseline representation.
2. Run **Week 6** to build and evaluate the global steering direction.
3. Run **Week 7** to compare global vs local directions.
4. Run **Week 8** to evaluate automatic safe step selection.
5. Run **Week 9** for the `β`-VAE comparison.
6. Run **Week 10** for the dSprites OOD extension.

In practice:

```bash
jupyter notebook
```

Then open the relevant notebook and execute cells top-to-bottom.

---

## Evaluation logic

The project does **not** treat a visually noticeable edit as sufficient. Steering is evaluated through explicit metrics.

### MNIST metrics

- **Probe margin / gain** — did the thickness attribute increase?
- **Digit accuracy** — was digit identity preserved?
- **Digit consistency** — did the predicted digit stay unchanged after editing?
- **Feature drift** — how far did the edit move away from the reconstruction in feature space?

### dSprites metrics

- **Scale gain** — did scale increase as intended?
- **Shape accuracy / consistency** — was shape preserved?
- **Image drift** — how far did the steered image move from the reconstruction?

This is what makes the project about **reliable controllability**, not just latent traversals.

---

## Why the project matters

Many latent-edit demos look convincing because they move in some direction and change the image. That is not enough. A useful edit should be:

- **targeted** — the intended factor changes,
- **stable** — the edit behaves predictably across samples,
- **safe** — preserved content stays preserved,
- **transferable** — the method does not collapse outside the easiest split.

This repository studies exactly that transition: from simple latent arithmetic to **constraint-aware controllable editing**.

---

## Final paper

The consolidated write-up is included here:

- `latent_steering_paper_v6.pdf`

It summarizes the full research trajectory, key tables, proposition-level framing, and the final interpretation of the results.

---

## Limitations

This repository is intentionally transparent about its limits:

- the codebase is notebook-based rather than a cleaned research package;
- some later-week results are summarized in memos and the final paper rather than exported as a fully standardized benchmark suite inside the repo root;
- probe quality and representation quality still limit how far steering can be trusted;
- the OOD setting in dSprites is meaningful, but still relatively controlled.

These limits are part of the project, not something hidden from it.

---

## Possible next steps

Good follow-up directions include:

- replacing proxy safety metrics with stronger semantic similarity constraints,
- learning directions with explicit preservation objectives,
- extending from notebook experiments to a reproducible benchmark script pipeline,
- testing on richer datasets with stronger attribute supervision or disentanglement.

---

## License

This repository is released under the MIT License. See `LICENSE` for details.
