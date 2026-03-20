# Weekly memo — dSprites OOD extension

## What was done
- Trained a compact dSprites VAE on ID orientations only.
- Trained latent heads for shape and scale.
- Built one global latent direction for increasing scale.
- Evaluated fixed-alpha and auto-alpha steering on ID and OOD splits.

## Dataset split
| split    |   n_samples |   n_groups_approx |
|:---------|------------:|------------------:|
| train    |       21000 |              3500 |
| val      |        3600 |               600 |
| id_test  |        4800 |               800 |
| ood_test |        4800 |               800 |

## Probe quality
| split    | target   |      acc |    n |
|:---------|:---------|---------:|-----:|
| id_test  | shape    | 0.746042 | 4800 |
| ood_test | shape    | 0.705208 | 4800 |
| id_test  | scale    | 0.507917 | 4800 |
| ood_test | scale    | 0.510417 | 4800 |

## Main observations
- Shape probe accuracy: ID = 0.7460, OOD = 0.7052.
- Scale probe accuracy: ID = 0.5079, OOD = 0.5104.
- Best fixed alpha on ID: alpha = 2.00, scale gain = 2.0283, shape consistency = 0.4658, drift = 10.7247.
- Same fixed alpha on OOD: scale gain = 2.0992, shape consistency = 0.4602, drift = 10.7588.
- Best auto setting on ID: tau_img = 7.00, scale gain = 0.8811, shape consistency = 1.0000, alpha_nonzero_rate = 0.7317.
- Same auto setting on OOD: scale gain = 0.8936, shape consistency = 1.0000, alpha_nonzero_rate = 0.7323.

## Interpretation
- If OOD shape consistency stays close to ID while scale gain remains positive, the steering direction transfers beyond the training support.
- If OOD gain collapses or shape consistency drops, failure is likely tied to representation shift across held-out orientations.

## Risks
- The result may depend strongly on VAE quality and latent disentanglement.
- OOD here is small: only orientation support shifts, not a new dataset.
- Fixed-alpha may over-edit on OOD even if it looks fine on ID.

## Next step
- Compare the same steering recipe against a stronger local direction or nearest-neighbor-conditioned direction on dSprites.