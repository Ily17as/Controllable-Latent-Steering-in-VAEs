# Week 5 — Weekly Update (1 page)

## Goal
Make the baseline fully working & reproducible: Conv-VAE (MNIST, latent dim = 16) + digit classifier (content preservation + feature extractor f(x)) + stroke-thickness probe s(x) with deterministic runs and saved artifacts.

## What I ran
- seed: 42
- device: cuda (actual: cuda)
- VAE: z=16, beta=1.0, epochs=20
- CLF epochs=5
- PROBE epochs=5 thin_k=1 thick_k=1

## Results (key)
- clf_best_acc: 0.9909
- probe_best_acc: 0.9893
- vae_last_val_loss: 100.2445

## Key plots
- plots/vae_loss_curve.png
- plots/recon_ep*.png
- plots/clf_curve.png
- plots/probe_curve.png
- plots/thickness_aug_preview.png

## Quick interpretation
- VAE: loss decreases smoothly; train/val curves stay close; reconstructions are stable and recognizable by ~epoch 5–10.
- Classifier: near-99% MNIST accuracy; usable as a content-preservation signal and as f(x).
- Probe: high accuracy on thin vs thick; next step is to verify it is not solving the task via a trivial “ink area” shortcut only.

## Next week plan (Week 6)
- Learn global steering directions in latent space and evaluate: probe score ↑ vs digit accuracy ↓ (content preservation trade-off).
- Add a cleaner evaluation protocol:
  - digit_acc on original vs augmented vs steered reconstructions
  - probe score on reconstructions (not only on augmented inputs)
- Add one sanity metric for thickness augmentation (e.g., mean ink_gap between thin/thick).

## Risks / issues
- Probe shortcut risk: it may rely on a simple global statistic (e.g., total “ink area”) rather than thickness structure → mitigate by randomizing augmentation strength per sample and monitoring ink_gap + digit accuracy.
- Over-steering risk (next week): improving thickness score may degrade digit identity → track digit_acc/f(x) similarity.
- Notebook environment: DataLoader multiprocessing can be noisy in some setups → prefer num_workers=0 or persistent spawn workers for stability.
