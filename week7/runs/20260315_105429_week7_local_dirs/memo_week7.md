# Weekly Update (Stage 3 — Local Directions via Clustering)

## Goal
Test the piecewise-linearity hypothesis by replacing one global steering direction with cluster-specific local directions learned in latent space.

## What was implemented
- Extract latent means z for the train set.
- Fit k-means on z for multiple K values.
- Learn one local thickness direction d_k per cluster from paired thin/thick latent deltas.
- Evaluate global vs local steering on the same alpha sweep using digit accuracy, probe margin, feature-space change, and monotonicity.

## Main run configuration
- seed: 42
- device: cpu
- latent_dim: 16
- K values: [2, 4, 8, 16]
- alpha sweep: [-4.0, -2.0, -1.0, -0.5, 0.0, 0.5, 1.0, 2.0, 4.0]
- ink_gap_mean on train augmentations: 0.1869

## Cluster diagnostics
- K=2: min cluster=14259, max cluster=17741, mean cluster=16000.0, fallbacks=0.
- K=4: min cluster=6832, max cluster=8964, mean cluster=8000.0, fallbacks=0.
- K=8: min cluster=2833, max cluster=5252, mean cluster=4000.0, fallbacks=0.
- K=16: min cluster=1362, max cluster=3343, mean cluster=2000.0, fallbacks=0.

## Quantitative summary
- global: mean monotonicity=0.9885, fallback_count=0.
- local_k2: mean monotonicity=0.9892, fallback_count=0.
- local_k4: mean monotonicity=0.9891, fallback_count=0.
- local_k8: mean monotonicity=0.9897, fallback_count=0.
- local_k16: mean monotonicity=0.9899, fallback_count=0.

## Best local-vs-global trade-off under a small content-loss budget
Best family: **local_k16** at alpha=1.00 with digit_acc=0.9673, probe_margin=2.2933, feature_L2=4.6218, acc_drop_vs_recon=0.0049.

## Interpretation
If a local family beats the global family at similar digit accuracy but higher probe margin, that supports the piecewise-linear hypothesis. If not, then the latent space may already be close to globally linear for this attribute, or the current clustering is not aligned with the semantic regions.

## Next week plan
- Add automatic safe step selection alpha* using the current feature penalty / digit-accuracy constraints.
- Compare fixed alpha vs alpha* on the best local family.
- Inspect failure cases where the probe improves but digit identity breaks.

## Risks / limitations
- k-means partitions z by geometry, not necessarily by semantics; local clusters may not align with the true editing regimes.
- Some clusters may be small, which makes d_k noisy; fallback-to-global avoids crashes but weakens locality.
- The thickness probe may still partially reward simple ink-area changes rather than pure stroke-thickness semantics.