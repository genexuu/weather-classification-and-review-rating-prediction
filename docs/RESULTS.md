# Weather Classification and Review Rating Prediction — Results

[English overview](../README.md) | [Contributions](CONTRIBUTIONS.md)

All numerical results below are historical report values. Public and Private scores have not been independently checked against a leaderboard during portfolio preparation. Different evaluation protocols are presented separately so that their numbers retain their original meaning.

## Task 1: weather classification

### Data and implementation

The dataset contains 9,900 labelled rows, 3,300 test rows, 10 raw attributes, and four classes.

The implementation combines:

- A handcrafted 18-feature base branch and a 51-feature engineered branch.
- Custom CART and random forests using Gini/entropy criteria.
- Gradient/Hessian-based multiclass boosting.
- Partition-aware calibration.
- A final extended-branch blend with weights **0.45 / 0.35 / 0.20**.

Implementation and aggregate experiment records are retained in the private source repository.

### Evaluation protocols

| Historical evaluation | Macro-F1 | Interpretation |
| --- | --- | --- |
| Public result | 0.92456 | Historical report value; no verified Private score is available. |
| `run_all` development-set five-fold CV | 0.9146 +/- 0.0050 | Development-set cross-validation summary. |
| `run_all` separate 20% holdout | 0.9273 | Holdout excluded from the 80% development split. |

The `run_all` development results are distinct from the separate full-training OOF and ablation tables. Their sample scope and evaluation protocol must not be treated as interchangeable; no additional OOF or ablation values are asserted here.

The source README cautions that the balanced variant's result-to-source mapping is uncertain. It is therefore not presented as a verified result for the exact code.

### Interpretation and limitations

Dataset-specific boundaries and repeated leaderboard tuning limit external generalization. A strong historical classification score does not establish performance on other weather datasets or future observations. This work is a weather classification experiment, not a demonstrated deployed forecasting service.

## Task 2: review rating prediction

### Data audit and split design

The data audit records **7,684,645 training rows** and **10,000 test rows**.

The preparation pipeline uses streaming typed cleaning followed by Spark aggregate validation. Stable predictor-group SHA256 keys support a split with no duplicate predictor groups across training and validation. This is a predictor-group-disjoint split: users and products may occur on both sides, so it does not measure a strict unseen-user or unseen-product setting.

Zhen's grouped holdout contains:

| Partition | Records |
| --- | ---: |
| Training | 6,147,416 |
| Validation | 1,537,229 |
| Total | 7,684,645 |

### Same-split bias models

The baseline pipeline uses no text features. It estimates a global mean, then sequential regularized grouped residual aggregates for parent and user biases. The full-weight prediction is:

```text
prediction = clip(mu + parent_bias + user_bias, 1, 5)
```

The user-weight experiment scales the user-bias term; weight 1 corresponds to the formula above.

| Historical model | Grouped-holdout RMSE |
| --- | ---: |
| Global mean | 1.516148 |
| Parent bias only | 1.487245 |
| Parent + user bias, user weight 0.5 | 1.480417 |
| Parent + user bias, user weight 1 | 1.477608 |

These four values share the same grouped holdout and support a direct comparison within that experiment. They must not be compared with the advanced five-fold OOF results as if they measured improvement on the same split.

### Advanced historical model sequence

Every entry in this table is a **historical report value**, including OOF measurements. Public and Private entries are not independently checked leaderboard scores. The advanced final score is a team result; [contribution details](CONTRIBUTIONS.md) distinguish pipeline and ensemble ownership.

| Model | five-fold OOF RMSE | Public RMSE | Private RMSE |
| --- | ---: | ---: | ---: |
| LightGBM 31 leaf 2000 rounds | 1.211440 | 0.85062 | 0.85760 |
| LightGBM 31 leaf 4000 cap | 1.205501 | 0.83502 | 0.84503 |
| LightGBM 63 leaf | 1.200075 | 0.81993 | 0.83438 |
| Word TextCNN + tree | 1.158405 | 0.72330 | 0.72926 |
| BPE + word + tree | 1.155815 | 0.71762 | 0.72454 |
| history frequency | 1.151259 | 0.71098 | 0.71742 |
| rating history | 1.150994 | 0.71051 | 0.71588 |
| compact Transformer blend | 1.145156 | 0.69682 | 0.70354 |
| plus q25 | Not measured | 0.53709 | 0.54560 |

### Transformer blend selection

The blend coefficient **alpha = 0.453822** was selected on fold0. Folds1–4 were excluded from weight selection; over their **6,146,791 records**, historical RMSE changed from **1.151347 to 1.145512**.

The pooled five-fold OOF RMSE of **1.145156** includes fold0, the weight-selection fold. The folds1–4 result therefore provides a distinct view of performance outside the fold used to choose the blend weight; the pooled value is not entirely independent of that selection.

### q25 adjustment and evidence limits

For a prediction `r`, q25 applies:

```text
adjusted_prediction = clip(3 + (r - 3) / 0.75, 1, 5)
```

This adjustment assumes label noise. Title-based evidence is a diagnostic, not proof of the true labels. There is no independent human-rated test set and no q25 OOF measurement. Its historical Public/Private values should be interpreted within those limits, not as evidence of general rating accuracy.

The Task 2 benchmark also does not demonstrate arbitrary new-review inference from the compact artifacts.

## Historical execution context

The bias pipeline ran with Spark `local[2]` on a single Windows host, using two worker threads, an i7-12700H, and a 4 GiB driver.

| Historical bias-pipeline measurement | Seconds |
| --- | ---: |
| Offline preprocessing + training | 643.536 |
| Fit-only, included in the offline measurement | 28.123 |
| 10,000 test predictions through CSV | 6.093 |
| Complete wall time | 661.596 |

These measurements describe different scopes; the fit-only time is not an additional phase to add to preprocessing plus training. The complete wall measurement covers the overall run.

Later Spark feature work used `local[4]`; neural models used one T4 per model. There is no demonstrated multi-server speedup or unified end-to-end timing for the advanced ensemble. Quick replay duration is not a training or inference benchmark.

## Verification boundary

The original implementation was checked using authorized private inputs. The weather pipeline reproduced both archived prediction CSVs byte for byte; the review pipeline verified three historical output fingerprints through saved-parameter inference and saved-artifact replay. Full Spark/GPU retraining was not performed. See the [verification summary](VERIFICATION.md).

This public repository contains documentation only and cannot execute the private pipelines. Historical leaderboard values were not independently rechecked online.
