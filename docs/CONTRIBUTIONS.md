# Weather Classification and Review Rating Prediction — Contributions

[English overview](../README.md) | [Results](RESULTS.md)

The team worked on weather classification and large-scale review rating prediction. The attribution below follows the original submission and source descriptions.

## Submitted attribution

The original submission records four equal contributions:

| Contributor | Submitted share | Documented focus |
| --- | ---: | --- |
| Zhen Xu | 25% | Task 2 data audit, Spark validation, grouped holdout, and bias models |
| Haobo Sun | 25% | Task 2 advanced feature, tree, neural, and calibration work |
| Kunyang Du | 25% | Task 1 Part B: data, validation, and optimization |
| ALE Manisa | 25% | Task 1 Part A: model work |

These percentages are submitted attribution, not independently measured effort. They do not quantify individual ownership of every file or model stage.

## Task 1: weather classification

The Task 1 source identifies **ALE Manisa** with **Part A / model work** and **Kunyang Du** with **Part B / data, validation, and optimization**.

The combined task includes handcrafted feature branches, custom CART/random forest/boosting implementations, partition-aware calibration, and ensemble optimization. The available attribution supports the Part A/Part B division above; it does not establish an individual author for every implementation detail.

The historical Public Macro-F1 of **0.92456** is presented as a Task 1 team result. No Private score is verified. See [results and evaluation boundaries](RESULTS.md) for the separate development-set and full-training evaluation scopes.

## Task 2: Zhen Xu

The report documents Zhen's work on the data and interpretable modeling foundation:

- Audited streaming typed cleaning for 7,684,645 training records and 10,000 test records.
- Spark aggregate quality checks.
- Exact predictor-group splitting with stable SHA256 keys, preventing duplicate predictor groups across training and validation.
- A global mean baseline and sequential regularized parent/user bias models without text features.
- Evaluation metrics and execution timing for that pipeline.

The grouped holdout contains **6,147,416 training** and **1,537,229 validation** records. Users and products may appear on both sides; predictor-group separation does not imply unseen-user or unseen-product evaluation.

On this same split, historical RMSE was **1.516148** for the mean baseline, **1.487245** for parent bias only, **1.480417** with user weight 0.5, and **1.477608** with user weight 1.

This evidence supports a personal claim about the millions-of-records preparation pipeline and the same-split bias-model improvement. It does not attribute the entire advanced final ensemble to Zhen.

### Resume bullet

- Built a streaming data-cleaning and Spark validation pipeline for 7.68 million review records, implemented a predictor-group-disjoint holdout, and reduced same-split RMSE from 1.516148 to 1.477608 with regularized parent/user bias models.

## Task 2: Haobo Sun

Haobo reused the cleaning and review-group foundation, then developed the advanced modeling sequence:

- Sparse features and LightGBM models.
- Word and BPE TextCNN models combined with tree predictions.
- History-frequency and rating-history calibration.
- A compact Transformer and prediction blending.
- The q25 adjustment under a label-noise assumption.

The Transformer blend weight was selected on fold0, while folds1–4 were excluded from weight selection. q25 has no measured OOF result or independent human-rated test set. These distinctions matter when describing the advanced work; the full numerical sequence and limitations appear in [RESULTS.md](RESULTS.md).

## Describing the final team result

Task 2's advanced final historical Public/Private RMSE of **0.53709 / 0.54560** is a **team result**, with advanced ensemble and q25 development attributed to Haobo on top of the reused foundation. It is not solely Zhen's work, and the leaderboard values have not been independently checked during portfolio preparation.

The initial bias-model grouped holdout and advanced five-fold OOF use different evaluation protocols. The advanced final score cannot be used as the endpoint of Zhen's same-split baseline improvement claim.

## Source access and verification

The full implementation and original records remain private. This public showcase describes the work and its attribution; it does not distribute runnable code, notebooks or datasets. The [verification summary](VERIFICATION.md) explains checks performed with private inputs and their limits.
