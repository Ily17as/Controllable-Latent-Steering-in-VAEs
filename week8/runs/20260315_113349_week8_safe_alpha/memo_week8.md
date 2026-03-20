# Weekly Update (Stage 4 — Automatic Safe Step Selection alpha*)

## Goal
Replace a single fixed steering step with an automatic per-sample choice alpha* that increases the target attribute while respecting a content-preservation budget.

## What was implemented
- Rebuild global and local direction families from the Week 5 VAE + probe pipeline.
- Evaluate fixed alpha baselines on the test set.
- For each sample, scan a small alpha grid and choose the feasible alpha* maximizing target_gain - lambda_feat * feat_penalty - lambda_conf * conf_drop.
- Use early stopping when penalties exceed the allowed budget.
- Report auto alpha* vs fixed-step reliability across multiple feature-drift budgets.

## Main configuration
- alpha_grid: [0.0, 0.25, 0.5, 0.75, 1.0, 1.25, 1.5, 2.0, 2.5, 3.0, 4.0]
- fixed_alphas: [0.5, 1.0, 2.0]
- tau_feat_values: [2.0, 3.5, 5.0]
- tau_conf: 0.2
- lambda_feat: 0.15
- lambda_conf: 1.0
- strict_digit_consistency: True
- ink_gap_mean on train augmentations: 0.1869

## Automatic alpha* summary
- global @ tau=2.00: mean_alpha*=0.328, nonzero_rate=0.897, digit_acc=0.9722, consistency=1.0000, probe_gain=0.8683, feat_L2=1.3668, objective=0.6622.
- global @ tau=3.50: mean_alpha*=0.691, nonzero_rate=0.957, digit_acc=0.9722, consistency=1.0000, probe_gain=1.8380, feat_L2=2.8013, objective=1.4153.
- global @ tau=5.00: mean_alpha*=1.066, nonzero_rate=0.966, digit_acc=0.9722, consistency=1.0000, probe_gain=2.8420, feat_L2=4.2199, objective=2.2051.
- local_k16 @ tau=2.00: mean_alpha*=0.321, nonzero_rate=0.891, digit_acc=0.9722, consistency=1.0000, probe_gain=0.8710, feat_L2=1.3567, objective=0.6664.
- local_k16 @ tau=3.50: mean_alpha*=0.686, nonzero_rate=0.963, digit_acc=0.9722, consistency=1.0000, probe_gain=1.8673, feat_L2=2.8144, objective=1.4427.
- local_k16 @ tau=5.00: mean_alpha*=1.071, nonzero_rate=0.971, digit_acc=0.9722, consistency=1.0000, probe_gain=2.9198, feat_L2=4.2609, objective=2.2768.
- local_k2 @ tau=2.00: mean_alpha*=0.328, nonzero_rate=0.898, digit_acc=0.9722, consistency=1.0000, probe_gain=0.8678, feat_L2=1.3646, objective=0.6620.
- local_k2 @ tau=3.50: mean_alpha*=0.695, nonzero_rate=0.959, digit_acc=0.9722, consistency=1.0000, probe_gain=1.8487, feat_L2=2.8098, objective=1.4247.
- local_k2 @ tau=5.00: mean_alpha*=1.075, nonzero_rate=0.968, digit_acc=0.9722, consistency=1.0000, probe_gain=2.8643, feat_L2=4.2382, objective=2.2246.
- local_k4 @ tau=2.00: mean_alpha*=0.327, nonzero_rate=0.898, digit_acc=0.9722, consistency=1.0000, probe_gain=0.8665, feat_L2=1.3640, objective=0.6608.
- local_k4 @ tau=3.50: mean_alpha*=0.693, nonzero_rate=0.960, digit_acc=0.9722, consistency=1.0000, probe_gain=1.8462, feat_L2=2.8107, objective=1.4222.
- local_k4 @ tau=5.00: mean_alpha*=1.073, nonzero_rate=0.968, digit_acc=0.9722, consistency=1.0000, probe_gain=2.8653, feat_L2=4.2401, objective=2.2255.
- local_k8 @ tau=2.00: mean_alpha*=0.327, nonzero_rate=0.894, digit_acc=0.9722, consistency=1.0000, probe_gain=0.8823, feat_L2=1.3641, objective=0.6766.
- local_k8 @ tau=3.50: mean_alpha*=0.697, nonzero_rate=0.963, digit_acc=0.9722, consistency=1.0000, probe_gain=1.8884, feat_L2=2.8245, objective=1.4622.
- local_k8 @ tau=5.00: mean_alpha*=1.081, nonzero_rate=0.971, digit_acc=0.9722, consistency=1.0000, probe_gain=2.9347, feat_L2=4.2603, objective=2.2918.

## Fixed-step reference
- global @ tau=2.00, fixed a=1.0: digit_acc=0.9671, consistency=0.9844, probe_gain=2.6276, feat_L2=4.6117, feasible_rate=0.0027.
- global @ tau=3.50, fixed a=1.0: digit_acc=0.9671, consistency=0.9844, probe_gain=2.6276, feat_L2=4.6117, feasible_rate=0.2729.
- global @ tau=5.00, fixed a=1.0: digit_acc=0.9671, consistency=0.9844, probe_gain=2.6276, feat_L2=4.6117, feasible_rate=0.6588.
- local_k16 @ tau=2.00, fixed a=1.0: digit_acc=0.9673, consistency=0.9849, probe_gain=2.7005, feat_L2=4.6218, feasible_rate=0.0027.
- local_k16 @ tau=3.50, fixed a=1.0: digit_acc=0.9673, consistency=0.9849, probe_gain=2.7005, feat_L2=4.6218, feasible_rate=0.2726.
- local_k16 @ tau=5.00, fixed a=1.0: digit_acc=0.9673, consistency=0.9849, probe_gain=2.7005, feat_L2=4.6218, feasible_rate=0.6530.
- local_k2 @ tau=2.00, fixed a=1.0: digit_acc=0.9676, consistency=0.9850, probe_gain=2.6362, feat_L2=4.5944, feasible_rate=0.0028.
- local_k2 @ tau=3.50, fixed a=1.0: digit_acc=0.9676, consistency=0.9850, probe_gain=2.6362, feat_L2=4.5944, feasible_rate=0.2757.
- local_k2 @ tau=5.00, fixed a=1.0: digit_acc=0.9676, consistency=0.9850, probe_gain=2.6362, feat_L2=4.5944, feasible_rate=0.6625.
- local_k4 @ tau=2.00, fixed a=1.0: digit_acc=0.9673, consistency=0.9851, probe_gain=2.6390, feat_L2=4.5961, feasible_rate=0.0022.
- local_k4 @ tau=3.50, fixed a=1.0: digit_acc=0.9673, consistency=0.9851, probe_gain=2.6390, feat_L2=4.5961, feasible_rate=0.2748.
- local_k4 @ tau=5.00, fixed a=1.0: digit_acc=0.9673, consistency=0.9851, probe_gain=2.6390, feat_L2=4.5961, feasible_rate=0.6644.
- local_k8 @ tau=2.00, fixed a=1.0: digit_acc=0.9677, consistency=0.9856, probe_gain=2.6936, feat_L2=4.5867, feasible_rate=0.0031.
- local_k8 @ tau=3.50, fixed a=1.0: digit_acc=0.9677, consistency=0.9856, probe_gain=2.6936, feat_L2=4.5867, feasible_rate=0.2840.
- local_k8 @ tau=5.00, fixed a=1.0: digit_acc=0.9677, consistency=0.9856, probe_gain=2.6936, feat_L2=4.5867, feasible_rate=0.6611.

## Best setting
Best automatic setting: **local_k8 @ tau=5.00** with mean_alpha*=1.081, digit_acc=0.9722, consistency=1.0000, probe_gain=2.9347, feat_L2=4.2603, objective=2.2918.
Against fixed a=1.0 at the same budget, auto alpha* changes probe_gain by +0.2411, digit_acc by +0.0045, and feasible_rate by +0.3389.

## Interpretation
If auto alpha* achieves similar or better probe gain with higher consistency / lower feature drift than a fixed alpha, then constraint-based steering improves reliability. If alpha* often collapses to zero, the direction is likely too entangled or the budget is too strict.

## Next week plan
- Run the focused beta-VAE comparison using the same safe alpha* evaluation protocol.
- Check whether stronger KL regularization improves monotonicity or merely reduces reconstruction quality.
- Inspect failures where the probe score improves but alpha* stays near zero because content constraints activate too early.

## Risks / limitations
- The current safety signal is proxy-based: feature drift and classifier confidence are useful but not a full semantic similarity metric.
- Early stopping assumes penalties usually grow with alpha; if trajectories are highly non-monotone, the scan can miss a later feasible point.
- The selected alpha* depends on the probe quality; a biased probe can distort the objective.