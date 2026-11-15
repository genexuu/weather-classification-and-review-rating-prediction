# Verification summary

The private implementation was checked on 15 November 2026. This public repository publishes a summary of those checks, not runnable source or private artifacts.

## Weather classification

A fresh isolated environment ran development five-fold cross-validation, a separate 20% holdout, full training and test prediction. Environment: Windows, Python 3.12.3, NumPy 2.5.4, pandas 3.1.1 and scikit-learn 1.9.1.

- Development Macro-F1: **0.9146 +/- 0.0050** at the runner's display precision.
- Holdout Macro-F1: **0.9273** at the runner's display precision.
- Both prediction outputs contained **3,300 rows** and matched their archived counterparts byte for byte.
- The additional experiment sweep and separate full-training OOF analysis were not rerun.

## Review rating prediction

Using Python 3.12.14 and NumPy 1.26.4, all three outputs matched their historical SHA-256 fingerprints. Each contained **10,000 unique test IDs** in template order, with finite ratings in `[1, 5]`.

The bias route recomputed predictions from saved parameter tables. The two advanced outputs reconstructed saved predictions and fold correction vectors. These checks do not establish fresh Spark/GPU training, neural inference on arbitrary reviews or a new leaderboard score.

Five existing data-validation regression tests passed in both normal and optimized Python execution. They cover dirty inputs, corrupt typed/group fields, integrity guards and tampered validation records.

## Documentation and publication

An independent document review checked metric scope, attribution and execution claims. Publication uses a new history containing only four English Markdown documents. Source code, executable notebooks, original reports, student identifiers, raw data, model artifacts and earlier repository history are excluded.

Historical results and personal contributions are described in [results](RESULTS.md) and [contributions](CONTRIBUTIONS.md). The detailed implementation and verification records remain private.
