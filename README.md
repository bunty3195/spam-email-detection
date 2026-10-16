# Email Spam Detection

An educational project exploring how to classify email as ham (not spam) or spam with Python, text preprocessing, TF-IDF, and traditional machine-learning models. The repository is a collection of experiments and saved artifacts, not a production application.

## Goals and learning objectives

- Inspect and prepare email datasets for text classification.
- Explore email text and class distributions.
- Convert text into sparse TF-IDF features.
- Train and evaluate Logistic Regression, Multinomial Naive Bayes, and Linear SVM classifiers.
- Learn to interpret accuracy, precision, recall, F1-score, and confusion matrices.

Random Forest has not been trained for this project and is not included as a completed model.

## Dataset preparation

The dataset-specific preparation notebooks describe Enron and SpamAssassin inputs. They clean each source into a common `email`, `label` format. The merge notebook combines those cleaned files, retains an optional source label for analysis, and removes duplicate emails. Its saved outputs show 35,806 rows in the final combined dataset. Labels are `0` for ham and `1` for spam.

The synthetic-data notebook is a separate experiment; the current merge and model workflow uses the Enron and SpamAssassin cleaned files. The repository does not document the original download URLs or dataset licenses, and the data files are excluded from Git, so obtain the data separately and verify its provenance and reuse terms.

## Text processing and TF-IDF

The source-specific notebooks prepare the email text and standardize the label columns. The TF-IDF workflow lowercases text, considers unigrams and bigrams, ignores terms appearing in fewer than five emails, and caps the vocabulary at 100,000 features. In model training, the vectorizer is fitted on the training split and then applied to the test split.

## Models and evaluation

The notebooks train and save the following models:

| Model | Artifact |
| --- | --- |
| Logistic Regression | `models/logistic_regression.pkl` |
| Multinomial Naive Bayes | `models/naive_bayes.pkl` |
| Linear SVM (`LinearSVC`) | `models/linear_svm.pkl` |
| Fitted TF-IDF vectorizer | `models/tfidf_vectorizer.pkl` |

Evaluation uses a single 80/20 train/test split with `random_state=42`; the notebook code does not stratify that split. The saved evaluation outputs report 28,644 training emails and 7,162 test emails.

| Model | Test accuracy |
| --- | ---: |
| Logistic Regression | 98.1988% |
| Multinomial Naive Bayes | 98.1011% |
| Linear SVM | 98.9807% |

These figures are transcribed from the saved outputs in [06_model_evaluation.ipynb](notebooks/06_model_evaluation.ipynb). That notebook also reports per-class precision, recall, F1-score, and confusion matrices. Results describe this one saved split; they are not a guarantee of performance on new data or a substitute for broader validation.

## Repository layout

```text
spam-email-detection/
├── app/
│   └── main.py                       # Planned UI entry point; currently empty
├── data/                             # Local data; ignored by Git
│   ├── raw/
│   ├── processed/
│   │   └── tfidf/                    # Generated feature matrices and labels
│   └── Combined/
├── models/
│   ├── logistic_regression.pkl
│   ├── naive_bayes.pkl
│   ├── linear_svm.pkl
│   └── tfidf_vectorizer.pkl
├── notebooks/
│   ├── 01_dataset_analysis.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_eda.ipynb
│   ├── 04_tfidf.ipynb
│   ├── 05_model_training.ipynb
│   ├── 06_model_evaluation.ipynb
│   ├── Merge_Dataset.ipynb
│   ├── enron_analysis_and_cleaning.ipynb
│   ├── spamassassin_analysis_and_cleaning.ipynb
│   └── synthetic_analysis_and_cleaning.ipynb
├── src/
│   ├── data/                         # clean.py and load.py: reserved data helpers
│   ├── features/tfidf.py             # Reserved feature extraction module
│   └── models/                       # evaluate.py, predict.py, train.py placeholders
├── tests/
│   ├── test_cleaning.py              # Empty test placeholder
│   └── test_prediction.py            # Empty test placeholder
├── .gitignore
└── requirements.txt
```

The `src/` modules and test files are currently empty placeholders; the working experiments are in the notebooks. The UI is under progress and is intended to be written in [`app/main.py`](app/main.py). No user interface is implemented yet.

## Setup

1. Clone the repository and open a terminal at its root.
2. Create and activate a virtual environment:

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   On macOS or Linux, activate it with `source .venv/bin/activate`.

3. Install the listed dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

4. Supply the data files locally. The current merge workflow expects:

   ```text
   data/raw/combined_enron_preprocessed.csv
   data/raw/combined_spamassassin_7_archives.csv
   ```

   Run the Enron and SpamAssassin preparation notebooks to create `data/processed/enron_clean.csv` and `data/processed/spamassassin_clean.csv`. The merge notebook writes the combined files under `data/Combined/`. None of these data files are included in a fresh clone because `/data/` is ignored by Git.

## Notebook guide

The numbered notebooks document a learning sequence: [01 dataset analysis](notebooks/01_dataset_analysis.ipynb), [02 data cleaning](notebooks/02_data_cleaning.ipynb), [03 EDA](notebooks/03_eda.ipynb), [04 TF-IDF](notebooks/04_tfidf.ipynb), [05 model training](notebooks/05_model_training.ipynb), and [06 model evaluation](notebooks/06_model_evaluation.ipynb).

For the current combined-data model workflow, prepare each source with [Enron analysis and cleaning](notebooks/enron_analysis_and_cleaning.ipynb) and [SpamAssassin analysis and cleaning](notebooks/spamassassin_analysis_and_cleaning.ipynb), combine them with [Merge Dataset](notebooks/Merge_Dataset.ipynb), then follow notebooks 04-06. [Synthetic analysis and cleaning](notebooks/synthetic_analysis_and_cleaning.ipynb) is a separate experiment and is not used by the current merge notebook.

Some earlier Enron-only notebooks (01-03) still refer to older input/output filenames that are not present in the repository. Their saved content and outputs are retained as historical learning work; they are not required to reproduce the combined-data model results. Open the notebooks in VS Code or Jupyter and run cells in order. For the relative `../data` and `../models` paths to resolve as written, use `notebooks/` as the notebook working directory.

## Data and model artifacts

- `data/` is ignored by Git. A fresh clone will not contain the local CSV files or TF-IDF matrices; provide the expected inputs before running the data and feature notebooks.
- TF-IDF matrices and their train/test labels are written to `data/processed/tfidf/`.
- The trained `.pkl` files are committed under `models/`. They were created with `joblib`; loading serialized scikit-learn models across different library versions may not be compatible.
- The saved model files do not replace the source datasets needed by the evaluation notebook, which recreates the train/test split from the combined CSV.

## Limitations and future work

- Dataset provenance and licensing details are not recorded in the repository.
- The reported metrics come from one unstratified split; use additional validation and source-specific evaluation before drawing broader conclusions.
- Older exploration notebooks refer to filenames absent from the current data layout.
- `src/`, `tests/`, and `app/main.py` have not yet been implemented. The UI is under progress; a reusable application, automated tests, broader evaluation, and model monitoring are possible future work.
- This educational model is not production-ready and cannot guarantee detection of every spam email.
