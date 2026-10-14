# 64-bit 2-XOR Arbiter PUF Modeling

This repository contains a notebook that models a 64-bit 2-XOR Arbiter Physical Unclonable Function (XOR-APUF) from observed challenge–response pairs (CRPs).

## What the notebook covers

- Parsing raw CRP records and checking the aggregated dataset
- Converting hexadecimal challenges to ±1 values
- Building the APUF parity-feature representation
- Creating stratified training, validation, and held-out test sets
- Establishing a majority-class and logistic-regression baseline
- Training a dense neural network with early stopping
- Building an explicit quadratic feature space for the 2-XOR structure
- Comparing models on validation metrics and reporting final held-out test metrics
- Plotting data-quality checks, training history, model comparisons, and a confusion matrix

## Repository file

- `2xor_apuf_github_ready.ipynb` — documented end-to-end project notebook.

## Dataset setup

The raw dataset is not bundled in this repository. Obtain the source data separately and respect the dataset's licensing and sharing terms. The notebook defaults to the following Google Colab path:

```text
/content/2XOR_64bit_X0_Y0_virticle_223A5.txt
```

Change `TXT_PATH` in the notebook to the actual location of your file before running it.

**Important:** the current label-processing routine assumes each response token encodes a 32-bit word. It calculates the fraction of set bits, averages that fraction for rows with the same challenge ID, and thresholds at 0.5. Verify this interpretation and the challenge bit ordering against the dataset documentation before presenting the labels as hardware ground truth.

## Environment

The notebook expects Python 3 and these packages:

```bash
pip install numpy pandas matplotlib scikit-learn tensorflow
```

Google Colab is a convenient environment because TensorFlow is typically available there. Run the notebook from top to bottom after setting the raw dataset path.

## Modeling approach

A 2-XOR APUF's response is nonlinear in the parity features. The notebook compares:

1. **Linear logistic-regression baseline** on the original 64 parity features.
2. **Dense neural network** that learns nonlinear relationships implicitly.
3. **Explicit quadratic model** that includes the constant term, linear terms, and unique pairwise feature interactions. Since each parity feature is ±1, each square term equals 1 and is redundant; the reduced feature space has 2,081 columns.

A degree-2 polynomial-kernel SVM is included as an optional, potentially time-consuming experiment and is disabled by default.

## Evaluation protocol

The notebook creates an 80% train / 10% validation / 10% test split, stratified by the response label. Validation metrics are used for model comparison and early stopping. The held-out test set is reserved for final evaluation of the two primary model families.

Metrics reported include accuracy, precision, recall, F1 score, a classification report, and a confusion matrix. Please populate the README with the values from a fresh full run rather than copying results from a previous notebook version.

## Important limitations

- The response-label parsing is based on an explicit encoding assumption that should be checked against the source data documentation.
- A random split evaluates generalization to held-out challenges from this dataset; it does not guarantee generalization to other devices or acquisition conditions.
- Predicting CRPs is behavioral modeling and does not, by itself, recover the true physical delay parameters of the two component APUFs.

## Suggested next steps

- Verify challenge and response encoding.
- Compare results with/without low-reliability challenges.
- Tune hyperparameters using validation data only.
- Add ROC-AUC / precision–recall curves when useful.
- Evaluate on a separate device or dataset.
