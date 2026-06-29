# Notebook Workflow Guide

This project is organized into six notebooks that build a Spark-based Amazon reviews pipeline from raw data to model analysis.

## Task 1: Download and inspect the raw dataset

What happens:
- Installs and initializes PySpark and supporting packages.
- Downloads the `McAuley-Lab/Amazon-Reviews-2023` dataset from Hugging Face.
- Loads `raw/review_categories/Books.jsonl` into Spark.
- Displays sample rows, schema, row and column counts, file size, column names, and data types.

What to do:
- Run this first to confirm Spark works and the raw data is accessible.
- Use it as the starting point for the rest of the pipeline.

Output:
- Local raw data folder: `amazon_reviews/`

## Task 2: Clean the data and prepare features

What happens:
- Loads the raw reviews dataset into Spark.
- Removes duplicate rows.
- Drops rows where `text` is null or blank.
- Limits the dataset to 11 million rows and saves the cleaned result.
- Converts `rating` into a binary label:
  - `1` for ratings `>= 4`
  - `0` otherwise
- Casts `verified_purchase` to integer.
- Builds a feature set from:
  - `helpful_vote`
  - `verified_purchase`
- Creates scaled features with `StandardScaler`.
- Splits the data into training and testing sets using an 80/20 split.
- Saves both splits for later model training.

What to do:
- Run this after Task 1.
- Make sure the cleaned dataset and train/test splits are generated before moving to model training.

Outputs:
- Cleaned dataset: `amazon_reviews_clean_11M/`
- Training split: `train_df/`
- Testing split: `test_df/`

## Task 3: Train and compare models

What happens:
- Loads the saved training and testing datasets.
- Creates evaluators for AUC, accuracy, precision, recall, and F1.
- Trains and tunes four classifiers:
  - Logistic Regression
  - Decision Tree
  - Random Forest
  - Linear SVM
- Uses `TrainValidationSplit` to search hyperparameters for the first three models.
- Generates predictions for the test set.
- Measures each model with the evaluation metrics above.
- Builds a comparison table and confusion-matrix-style summaries.
- Saves trained models, prediction files, and the comparison results.

What to do:
- Run this after Task 2 so the model input data exists.
- Use the saved model and prediction artifacts from this notebook in Task 5.

Outputs:
- Saved models: `lr_model/`, `dt_model/`, `rf_model/`, `svm_model/`
- Saved predictions: `lr_predictions/`, `dt_predictions/`, `rf_predictions/`, `svm_predictions/`
- Comparison results: `model_comparison_results/`

## Task 4: Check partitioning, caching, and Spark UI

What happens:
- Loads the cleaned dataset.
- Inspects partition count before and after coalescing to 8 partitions.
- Creates a 500,000-row sample.
- Demonstrates caching and persisting with `MEMORY_AND_DISK`.
- Prints the Spark UI link for monitoring.

What to do:
- Run this when you want to inspect performance behavior or verify Spark execution setup.
- Use it as an optimization and debugging notebook rather than a core modeling step.

## Task 5: Evaluate results and explain the model

What happens:
- Loads the saved models from Task 3.
- Loads the saved prediction datasets and comparison results.
- Builds confusion matrices for all four models.
- Plots ROC curves and precision-recall curves.
- Samples predictions to check stability.
- Re-evaluates the sampled predictions with the same metrics.
- Uses SHAP to explain the logistic regression model on a small sample.
- Draws a SHAP waterfall plot for one example.

What to do:
- Run this after Task 3 so the saved models and predictions are available.
- Use it to interpret model quality, compare performance, and understand why the logistic regression model makes its predictions.

## Task 6: Convert the cleaned dataset for external use

What happens:
- Loads the cleaned parquet dataset from `amazon_reviews/amazon_reviews_clean_11M`.
- Verifies the row count, schema, and sample rows.
- Removes the `images` column to make the dataset lighter and easier to export.
- Randomly samples 250,000 rows.
- Writes the sample out as a single CSV file in `tableau_dataset/`.

What to do:
- Run this after Task 2 if you need a lighter CSV version of the cleaned data for tools like Tableau or for sharing outside Spark.
- Use it as an export step rather than a modeling step.

Output:
- CSV export folder: `tableau_dataset/`

## Recommended Run Order

1. Task 1
2. Task 2
3. Task 3
4. Task 4
5. Task 5
6. Task 6
