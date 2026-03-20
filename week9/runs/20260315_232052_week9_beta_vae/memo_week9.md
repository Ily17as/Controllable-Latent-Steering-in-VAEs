# Week 9 — Weekly Update (1 page)

## Goal
Run a focused β-VAE comparison to test whether stronger KL regularization improves steering controllability, consistency, and safety, while explicitly reporting the reconstruction trade-off.

## What I ran
- betas: [1.0, 2.0, 4.0]
- latent_dim: 16
- epochs per beta: 12
- steering families: global + local_k8
- auto alpha grid: [0.0, 0.25, 0.5, 0.75, 1.0, 1.25, 1.5, 2.0]
- feature budgets: [3.5, 5.0]

## Reconstruction summary
- beta=1.0: digit_acc_recon=0.9569, recon_bce=77.2511, kl=24.7025
- beta=2.0: digit_acc_recon=0.9343, recon_bce=88.3267, kl=16.9365
- beta=4.0: digit_acc_recon=0.8590, recon_bce=108.2665, kl=9.7359

## Best fixed steering under the accuracy-drop budget
- beta=1.0, family=global, alpha=2.00, probe_gain=5.4169, digit_acc=0.9516, feat_l2=8.5321
- beta=1.0, family=local_k8, alpha=2.00, probe_gain=5.4969, digit_acc=0.9532, feat_l2=8.3689
- beta=2.0, family=global, alpha=2.00, probe_gain=4.4715, digit_acc=0.9409, feat_l2=7.5261
- beta=2.0, family=local_k8, alpha=2.00, probe_gain=4.6200, digit_acc=0.9414, feat_l2=7.5017
- beta=4.0, family=global, alpha=2.00, probe_gain=4.1840, digit_acc=0.9024, feat_l2=7.3702
- beta=4.0, family=local_k8, alpha=2.00, probe_gain=4.3257, digit_acc=0.9116, feat_l2=7.3535

## Best auto steering at tau_feat=5.0
- beta=1.0, family=local_k8, probe_gain=2.8035, digit_acc=0.9705, feat_l2=3.9269, mean_alpha*=0.9956
- beta=2.0, family=local_k8, probe_gain=2.7192, digit_acc=0.9646, feat_l2=3.9477, mean_alpha*=1.1420
- beta=4.0, family=local_k8, probe_gain=2.5007, digit_acc=0.9382, feat_l2=3.8728, mean_alpha*=1.1349

## Interpretation
- This week quantifies the core β-VAE trade-off: stronger KL regularization may improve latent structure and steering reliability, but can also degrade reconstruction quality.
- The key question is not whether larger beta is universally better, but whether there exists a moderate beta that improves controllability without paying too much in reconstruction fidelity.

## Next week plan
- Failure analysis: inspect cases where steering changes digit identity or produces weak / saturated edits.
- Add a small publishability extension (e.g. dSprites or a second controlled attribute).

## Risks
- Large beta may collapse reconstruction quality and make the comparison unfair unless the trade-off is reported explicitly.
- The fixed probe may still carry shortcut bias, so gains should be interpreted together with preview grids and class-preservation signals.